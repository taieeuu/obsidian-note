---
Date: 2024-06-02
---
## Bridge Network
```shell
$ docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "1847e179a316ee5219c951c2c21cf2c787d431d1ffb3ef621b8f0d1edd197b24",
        "Created": "2021-07-01T15:28:09.265408946Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "03494b034694982fa085cc4052b6c7b8b9c046f9d5f85f30e3a9e716fad20741": {
                "Name": "box1",
                "EndpointID": "072160448becebb7c9c333dce9bbdf7601a92b1d3e7a5820b8b35976cf4fd6ff",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            },
            "4f3303c84e5391ea37db664fd08683b01decdadae636aaa1bfd7bb9669cbd8de": {
                "Name": "box2",
                "EndpointID": "4cf0f635d4273066acd3075ec775e6fa405034f94b88c1bcacdaae847612f2c5",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
```
>[!info]
>我們可以在自己的電腦上輸入 `ipconfig`，我們會發現會有一個docker0，那我們再結合上述的概念，container就是台點腦，docker0就會是交換機，會自動分發 IP 位置等處理動作。

>[!note]
>`brctl` 使用前需要安装, 對於CentOS, 可以通過 `sudo yum install -y bridge-utils` 安装. 對於Ubuntu, 可以通過 `sudo apt-get install -y bridge-utils`

![[Pasted image 20240602083533.png]]
>[!info]
> veth1、veth2 其實是在docker0 裡面的，也就是說 container都會透過 docker0 做轉發封包動作。

```shell
$ brctl show
bridge name     bridge id               STP enabled     interfaces
docker0         8000.0242759468cf       no              veth8c9bb82
                                                        vethd8f9afb
```
> [!info]
> 可以看到 docker0 上有兩個 interfaces 也就是我們上面圖中的 veth1、 veth2。
### 容器對外通信
查看路由
```shell
$ ip route
default via 10.0.2.2 dev eth0 proto dhcp metric 100
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1
192.168.200.0/24 dev eth1 proto kernel scope link src 192.168.200.10 metric 101
```
>[!info]
>我們上述使用`docker network inspect bridge`命令查看到兩個docker container IP位址 172.17.0.2/16、172.17.0.3/16，但我們的 eth0 的地址並不是，他是 10.0.2.2 ，那我們數據出去之前，會需要做一個 [[NAT網路地址轉換]]，將我們的位址轉換成 10.0.2.2，然後再傳出去。那這個轉發規則是透過 `iptable` 來定義的。

iptable 轉送規則
```shell
$ sudo iptables --list -t nat
Chain PREROUTING (policy ACCEPT)
target     prot opt source               destination
DOCKER     all  --  anywhere             anywhere             ADDRTYPE match dst-type LOCAL

Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
DOCKER     all  --  anywhere            !loopback/8           ADDRTYPE match dst-type LOCAL

Chain POSTROUTING (policy ACCEPT)
target     prot opt source               destination
MASQUERADE  all  --  172.17.0.0/16        anywhere

Chain DOCKER (2 references)
target     prot opt source               destination
RETURN     all  --  anywhere             anywhere
```

我們可以先查看 network 列表
```shell
docker network ls
```

這裡的 bridge 就是我們的 docker0 
```shell
NETWORK ID     NAME              DRIVER    SCOPE
0a2bac87ef38   bridge            bridge    local
6aa7ba401e19   host              host      local
38d5b72ad6c0   mongo_replica     bridge    local
c82130602054   mongodb_default   bridge    local
da4d8b9d191c   none              null      local
```

創建 network 
```shell
docker network create -d bridge mybridge
```
>[!info]
>- `docker network create`：這是 Docker 的命令，用於創建一個新的網絡。
>- `-d bridge`：這個選項指定了網絡的驅動類型。`-d` 代表「driver」，而 `bridge` 是 Docker 的一種網絡類型。bridge 網絡是 Docker 容器的默認網絡類型，它允許容器通過一個虛擬網橋與主機系統進行通信，也能夠讓容器之間相互通信。
>- `mybridge`：這是你要創建的網絡的名稱。在這個例子中，網絡被命名為 `mybridge`。

運行容器並指定她的網路
```shell
docker container run -d --rm --name box3 --network mybridge busy /bin/sh -c ..
```
>[!info]
>- `--network` : 指定你要使用的 network

主動連結
```shell
docker network connect bridge box3
```
>[!info]
>- `docker network connect`：這是 Docker 的命令，用來將一個容器連接到一個網絡。
>- `bridge`：這是你想要連接的網絡的名稱。這裡指的是 Docker 的預設橋接網絡。不過，通常在使用自訂網絡時，你可能會使用自己創建的網絡名稱（例如上一個命令中創建的 `mybridge`）。需要確保這裡指定的網絡名稱正確。
>- `box3`：這是要連接到指定網絡的容器名稱或容器ID。這意味著 `box3` 將能夠通過 `bridge` 網絡與其他連接到同一網絡的容器進行通信。

我們整個的 network 架構也就是這樣，如下圖
![[Pasted image 20240602103030.png|600]]
### Bridge 注意事項
**在 docker 默認中的 Bridge 是沒有提供 DNS 域名解析 的功能，但在自定義的有提供。**
所以在一般會建議自己建立 network 做使用。
### 自己定義 subnet與gateway
```shell
docker network create -d bridge --gateway 172.200.0.1 --subnet 172.200.0.0/16 demo
```
>[!info]
>- `docker network create`：這是 Docker 的命令，用來創建一個新的網絡。
>- `-d bridge`：這個選項指定了網絡的驅動類型為 `bridge`。Bridge 是 Docker 默認的網絡類型，用於創建一個虛擬的網橋，讓容器能夠連接到一個單獨的網絡。
>- `--gateway 172.200.0.1`：指定網絡的網關地址。網關通常是容器訪問外部網絡（包括互聯網）的路由器地址。在這個設定中，所有通往網絡外的流量將會通過這個 IP 地址路由。  
>- `--subnet 172.200.0.0/16`：定義網絡的子網。這是一個 CIDR 表示法，指定了網絡的 IP 地址範圍。這裡 `/16` 表示網絡的前 16 位固定，提供了 65536 個可能的 IP 地址給容器使用。
>- `demo`：這是創建的網絡的名稱，用於在 Docker 環境中識別這個網絡。
#### 使用場景和效果
創建具有自定義子網和網關的網絡常用於以下情況：
- **大型應用部署**：當需要管理大量容器並且需要精細控制 IP 地址管理和路由策略時。
- **網絡隔離**：提供一個隔離的網絡環境，增強安全性和控制。
- **網絡策略實施**：通過指定子網和網關，可以更好地整合和控制容器網絡流量，例如限制或監控對特定外部資源的訪問。
### 容器端口轉發
![[Pasted image 20240602110815.png]]
>[!info]
>這兩台host1、host2是可以相互 ping 通的 nginx、busybox 也可以連接到 host2 ，因為他們可以透過 eth0 做 NAT 進行轉換，也就是使用 192.168.200.10 來訪問 host2，但是要注意 host2 無法訪問 nginx container 。

不過我們可以透過 host1 轉發 nginx container 的服務。
```shell
docker container run -d --rm --name -p 8080:80 web nginx
```
>[!info]
>轉發至host1:8080，讓我們的host2可以訪問。