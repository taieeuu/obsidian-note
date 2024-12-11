---
Date: 2024-06-04
tags:
  - docker
  - devops
---
## 內部負載平衡和 VIP
建立一個mynet的覆蓋網絡，建立一個服務
```shell
vagrant@swarm-manager:~$ docker network ls
NETWORK ID     NAME              DRIVER    SCOPE
afc8f54c1d07   bridge            bridge    local
128fd1cb0fae   docker_gwbridge   bridge    local
0ea68b0d28b9   host              host      local
14fy2l7a4mci   ingress           overlay   swarm
lpirdge00y3j   mynet             overlay   swarm
a8edf1804fb6   none              null      local
vagrant@swarm-manager:~$ docker service create --name web --network mynet --replicas 2 containous/whoami
jozc1x1c1zpyjl9b5j5abzm0g
overall progress: 2 out of 2 tasks
1/2: running   [==================================================>]
2/2: running   [==================================================>]
verify: Service converged
vagrant@swarm-manager:~$ docker service ls
ID             NAME      MODE         REPLICAS   IMAGE                      PORTS
jozc1x1c1zpy   web       replicated   2/2        containous/whoami:latest
vagrant@swarm-manager:~$ docker service ps web
ID             NAME      IMAGE                      NODE            DESIRED STATE   CURRENT STATE            ERROR     PORTS
pwi87g86kbxd   web.1     containous/whoami:latest   swarm-worker1   Running         Running 47 seconds ago
xbri2akxy2e8   web.2     containous/whoami:latest   swarm-worker2   Running         Running 44 seconds ago
vagrant@swarm-manager:~$
```

創建一個客戶端
```shell
vagrant@swarm-manager:~$ docker service create --name client --network mynet xiaopeng163/net-box:latest ping 8.8.8.8
skbcdfvgidwafbm4nciq82env
overall progress: 1 out of 1 tasks
1/1: running   [==================================================>]
verify: Service converged
vagrant@swarm-manager:~$ docker service ls
ID             NAME      MODE         REPLICAS   IMAGE                        PORTS
skbcdfvgidwa   client    replicated   1/1        xiaopeng163/net-box:latest
jozc1x1c1zpy   web       replicated   2/2        containous/whoami:latest
vagrant@swarm-manager:~$ docker service ps client
ID             NAME       IMAGE                        NODE            DESIRED STATE   CURRENT STATE            ERROR     PORTS
sg9b3dqrgru4   client.1   xiaopeng163/net-box:latest   swarm-manager   Running         Running 28 seconds ago
vagrant@swarm-manager:~$
```

試著進入client這個容器，ping web這個服務名，取得到的IP 10.0.1.30，稱為VIP去（虛擬IP）
```shell
vagrant@swarm-manager:~$ docker container ls
CONTAINER ID   IMAGE                        COMMAND          CREATED          STATUS          PORTS     NAMES
36dce35d56e8   xiaopeng163/net-box:latest   "ping 8.8.8.8"   19 minutes ago   Up 19 minutes             client.1.sg9b3dqrgru4f14k2tpxzg2ei
vagrant@swarm-manager:~$ docker container exec -it 36dc sh
/omd # curl web
Hostname: 6039865a1e5d
IP: 127.0.0.1
IP: 10.0.1.32
IP: 172.18.0.3
RemoteAddr: 10.0.1.37:40972
GET / HTTP/1.1
Host: web
User-Agent: curl/7.69.1
Accept: */*

/omd # curl web
Hostname: c3b3e99b9bb1
IP: 127.0.0.1
IP: 10.0.1.31
IP: 172.18.0.3
RemoteAddr: 10.0.1.37:40974
GET / HTTP/1.1
Host: web
User-Agent: curl/7.69.1
Accept: */*

/omd # curl web
Hostname: 6039865a1e5d
IP: 127.0.0.1
IP: 10.0.1.32
IP: 172.18.0.3
RemoteAddr: 10.0.1.37:40976
GET / HTTP/1.1
Host: web
User-Agent: curl/7.69.1
Accept: */*

/omd #
/omd # ping web -c 2
PING web (10.0.1.30): 56 data bytes
64 bytes from 10.0.1.30: seq=0 ttl=64 time=0.044 ms
64 bytes from 10.0.1.30: seq=1 ttl=64 time=0.071 ms

--- web ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.044/0.057/0.071 ms
/omd #
```

這個虛擬IP在一個特殊的網路命令空間裡，這個空間連接在我們的mynet這個覆蓋的網路上
透過 docker network inform mynet 可以看到這個命名空間，名為 lb-mynet
```shell
"Containers": {
"36dce35d56e87d43d08c5b9a94678fe789659cb3b1a5c9ddccd7de4b26e8d588": {
    "Name": "client.1.sg9b3dqrgru4f14k2tpxzg2ei",
    "EndpointID": "e8972d0091afaaa091886799aca164b742ca93408377d9ee599bdf91188416c1",
    "MacAddress": "02:42:0a:00:01:24",
    "IPv4Address": "10.0.1.36/24",
    "IPv6Address": ""
},
"lb-mynet": {
    "Name": "mynet-endpoint",
    "EndpointID": "e299d083b25a1942f6e0f7989436c3c3e8d79c7395a80dd50b7709825022bfac",
    "MacAddress": "02:42:0a:00:01:25",
    "IPv4Address": "10.0.1.37/24",
    "IPv6Address": ""
}
```

透過下面的命令，找到這個命名空間的名字
```shell
vagrant@swarm-manager:~$ sudo ls /var/run/docker/netns/
1-14fy2l7a4m  1-lpirdge00y  dfb766d83076  ingress_sbox  lb_lpirdge00
vagrant@swarm-manager:~$
```

名字叫`lb_lpirdge00`
透過輸入進入到這個命名空間的sh裡，可以看到剛才的VIP位址10.0.1.30
```shell
vagrant@swarm-manager:~$ sudo nsenter --net=/var/run/docker/netns/lb_lpirdge00 sh
#
# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
    valid_lft forever preferred_lft forever
50: eth0@if51: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default
    link/ether 02:42:0a:00:01:25 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.0.1.37/24 brd 10.0.1.255 scope global eth0
    valid_lft forever preferred_lft forever
    inet 10.0.1.30/32 scope global eth0
    valid_lft forever preferred_lft forever
    inet 10.0.1.35/32 scope global eth0
    valid_lft forever preferred_lft forever
#
```

和ingress網路一樣，可以查看iptables，ipvs的負載平衡，基本上就可以理解負載平衡是怎麼一回事了。 .1.32
```shell
# iptables -nvL -t mangle
Chain PREROUTING (policy ACCEPT 128 packets, 11198 bytes)
pkts bytes target     prot opt in     out     source               destination

Chain INPUT (policy ACCEPT 92 packets, 6743 bytes)
pkts bytes target     prot opt in     out     source               destination
72  4995 MARK       all  --  *      *       0.0.0.0/0            10.0.1.30            MARK set 0x106
    0     0 MARK       all  --  *      *       0.0.0.0/0            10.0.1.35            MARK set 0x107

Chain FORWARD (policy ACCEPT 36 packets, 4455 bytes)
pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 101 packets, 7535 bytes)
pkts bytes target     prot opt in     out     source               destination

Chain POSTROUTING (policy ACCEPT 128 packets, 11198 bytes)
pkts bytes target     prot opt in     out     source               destination
# ipvsadm
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
-> RemoteAddress:Port           Forward Weight ActiveConn InActConn
FWM  262 rr
-> 10.0.1.31:0                  Masq    1      0          0
-> 10.0.1.32:0                  Masq    1      0          0
FWM  263 rr
-> 10.0.1.36:0                  Masq    1      0          0
#
```

而這個流量會走我們的mynet這個覆蓋網路。

![[docker_vip.png]]
