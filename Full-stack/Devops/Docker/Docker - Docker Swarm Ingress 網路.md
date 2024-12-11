---
Date: 2024-06-04
tags:
  - docker
  - devops
---
## Docker Swarm Ingress 網路
docker swarm的ingress網路又叫`Ingress Routing Mesh`
主要是**為了實現把服務的服務連接埠對外發佈出去，讓其能夠被外部網路存取。**
入口路由網格是docker swarm網路中最複雜的部分內容，包含多方面的內容：
- iptables 的目標 NAT 流量轉發
- Linux 橋接器、網路命名空間
- 使用IPVS技術做負載平衡
- 包括容器間的通訊（overlay）和入方向流量的連接埠轉發
### 創建 Service
```shell
vagrant@swarm-manager:~$ docker service create --name web --network mynet -p 8080:80 --replicas 2 containous/whoami
a9cn3p0ovg5jcz30rzz89lyfz
overall progress: 2 out of 2 tasks
1/2: running   [==================================================>]
2/2: running   [==================================================>]
verify: Service converged
vagrant@swarm-manager:~$ docker service ls
ID             NAME      MODE         REPLICAS   IMAGE                      PORTS
a9cn3p0ovg5j   web       replicated   2/2        containous/whoami:latest   *:8080->80/tcp
vagrant@swarm-manager:~$ docker service ps web
ID             NAME      IMAGE                      NODE            DESIRED STATE   CURRENT STATE            ERROR     PORTS
udlzvsraha1x   web.1     containous/whoami:latest   swarm-worker1   Running         Running 16 seconds ago
mms2c65e5ygt   web.2     containous/whoami:latest   swarm-manager   Running         Running 16 seconds ago
vagrant@swarm-manager:~$
```
>[!info]
>建立一個服務，指定網路是overlay的mynet，透過-p把連接埠映射出來。
我們使用的鏡像`containous/whoami`是一個簡單的網路服務，可以返回伺服器的主機名，和基本的網路訊息，例如IP位址。
### 服務的存取
```shell
vagrant@swarm-manager:~$ curl 192.168.200.10:8080
Hostname: fdf7c1354507
IP: 127.0.0.1
IP: 10.0.0.7
IP: 172.18.0.3
IP: 10.0.1.14
RemoteAddr: 10.0.0.2:36828
GET / HTTP/1.1
Host: 192.168.200.10:8080
User-Agent: curl/7.68.0
Accept: */*

vagrant@swarm-manager:~$ curl 192.168.200.11:8080
Hostname: fdf7c1354507
IP: 127.0.0.1
IP: 10.0.0.7
IP: 172.18.0.3
IP: 10.0.1.14
RemoteAddr: 10.0.0.3:54212
GET / HTTP/1.1
Host: 192.168.200.11:8080
User-Agent: curl/7.68.0
Accept: */*

vagrant@swarm-manager:~$ curl 192.168.200.12:8080
Hostname: c83ee052787a
IP: 127.0.0.1
IP: 10.0.0.6
IP: 172.18.0.3
IP: 10.0.1.13
RemoteAddr: 10.0.0.4:49820
GET / HTTP/1.1
Host: 192.168.200.12:8080
User-Agent: curl/7.68.0
Accept: */*
```
>[!info]
>8080這個埠到底映射到哪裡了？
>可以看到三個節點IP都可以訪問，並且回應的容器是不同的（主機名稱），否則有負載平衡的效果。

![[Pasted image 20240606225220.png]]
### 入口資料包的走向
以manager節點為例，資料到底是如何達到服務的容器的。
```shell
vagrant@swarm-manager:~$ sudo iptables -nvL -t nat
Chain PREROUTING (policy ACCEPT 388 packets, 35780 bytes)
pkts bytes target     prot opt in     out     source               destination
296 17960 DOCKER-INGRESS  all  --  *      *       0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL
21365 1282K DOCKER     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL

Chain INPUT (policy ACCEPT 388 packets, 35780 bytes)
pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 340 packets, 20930 bytes)
pkts bytes target     prot opt in     out     source               destination
    8   590 DOCKER-INGRESS  all  --  *      *       0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL
    1    60 DOCKER     all  --  *      *       0.0.0.0/0           !127.0.0.0/8          ADDRTYPE match dst-type LOCAL

Chain POSTROUTING (policy ACCEPT 340 packets, 20930 bytes)
pkts bytes target     prot opt in     out     source               destination
    2   120 MASQUERADE  all  --  *      docker_gwbridge  0.0.0.0/0            0.0.0.0/0            ADDRTYPE match src-type LOCAL
    3   252 MASQUERADE  all  --  *      !docker0  172.17.0.0/16        0.0.0.0/0
    0     0 MASQUERADE  all  --  *      !docker_gwbridge  172.18.0.0/16        0.0.0.0/0

Chain DOCKER (2 references)
pkts bytes target     prot opt in     out     source               destination
    0     0 RETURN     all  --  docker0 *       0.0.0.0/0            0.0.0.0/0
    0     0 RETURN     all  --  docker_gwbridge *       0.0.0.0/0            0.0.0.0/0

Chain DOCKER-INGRESS (2 references)
pkts bytes target     prot opt in     out     source               destination
    2   120 DNAT       tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:8080 to:172.18.0.2:8080
302 18430 RETURN     all  --  *      *       0.0.0.0/0            0.0.0.0/0
```
>[!info]
>我們可以看到最後一行 docker-ingress ，可以看到他有一條規則 DNAT ，如果我們訪問**本地端的:8080端口**的話，會轉發到 `172.18.0.2:8080`。
>我們再看到其他台機器有確實都有這一條的規則

那這個172.18.0.2是什麼？
首先172.18.0.0/16這個網段是`docker_gwbridge`的，所以這個位址一定是連在了`docker_gwbridge`上的。
`docker network inspect docker_gwbridge`可以看到這個網路連接了一個叫　`ingress-sbox`　的容器。
這個　`ingress-sbox`　其實不是容器，而是一個網路的命名空間網路命名空間，我們可以透過下面的方式進入這個命名空間。

```shell
vagrant@swarm-manager:~$　docker run -it --rm -v /var/run/docker/netns:/netns --privileged=true nicolaka/netshoot nsenter --net=/netns/ingress_sbox sh
~ # ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
8: eth0@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default
    link/ether 02:42:0a:00:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.0.0.2/24 brd 10.0.0.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet 10.0.0.5/32 scope global eth0
       valid_lft forever preferred_lft forever
10: eth1@if11: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:12:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet 172.18.0.2/16 brd 172.18.255.255 scope global eth1
       valid_lft forever preferred_lft forever
```

透過查看位址，發現這個命名空間連接了兩個網絡，其中一個eth1連接了　`docker_gwbridge`　，另外一個eth0連接了　`ingress`這個網路。

```shell
~ # ip route
default via 172.18.0.1 dev eth1
10.0.0.0/24 dev eth0 proto kernel scope link src 10.0.0.2
172.18.0.0/16 dev eth1 proto kernel scope link src 172.18.0.2

~ # iptables -nvL -t mangle
Chain PREROUTING (policy ACCEPT 22 packets, 2084 bytes)
 pkts bytes target     prot opt in     out     source               destination
   12   806 MARK       tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:8080 MARK set 0x100

Chain INPUT (policy ACCEPT 14 packets, 1038 bytes)
 pkts bytes target     prot opt in     out     source               destination
    0     0 MARK       all  --  *      *       0.0.0.0/0            10.0.0.5             MARK set 0x100

Chain FORWARD (policy ACCEPT 8 packets, 1046 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 14 packets, 940 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain POSTROUTING (policy ACCEPT 22 packets, 1986 bytes)
 pkts bytes target     prot opt in     out     source               destination
~ # ipvsadm
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
FWM  256 rr
  -> 10.0.0.6:0                   Masq    1      0          0
  -> 10.0.0.7:0                   Masq    1      0          0
~ #
```

透過ipvs做了負載平衡
![[Pasted image 20240606230012.png]]
>[!info]
>關於這裡的負載平衡
>- 這是一個無狀態負載平衡
>- 這是三層的負載平衡，不是四層的負載平衡位於 OSI 第 3 層 (TCP)，而不是第 4 層 (DNS)
>- 以上限制可以透過Nginx或兩個HAProxy LB代理來解決（[https://docs.docker.com/engine/swarm/ingress/](https://docs.docker.com/engine/swarm/ingress/)）
