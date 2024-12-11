---
Date: 2024-02-24
tags:
  - "#docker"
  - devops
---
# Docker 常用指令
## Basic Grammar
### Download Image
```linux=
docker pull {image_name:version}
```
### Run Image
```linux=
docker run {image_name}
```

> 如果需要port，則需加-p。
> 如果需要背景執行 : -d。
### Delet Image
```linux=
docker rmi image_name
```
### Run Container(合併上述1 2)
```linux=
docker container run images_name
```
### Stop Container
```linux=
docker stop container_id
```
### Delet Container
```linux=
docker rm container_name
```
> image_name可更改為container_name

# Docker
## 幫助命令

~~~shell
docker version		#常看版本
docker info			#顯示docker系統訊息，包括鏡像
docker --help		#幫助命令
~~~

## **鏡像命令**

docker images 查看所有本地的主機上的鏡像

~~~shell
C:\Users\Tai>docker images
REPOSITORY             TAG                                   IMAGE ID       CREATED         SIZE
ch03-lab-soln          latest                                75fb590322c5   3 weeks ago     7.15MB
web-ping               latest                                46b29bc1ff35   3 weeks ago     75.3MB
web-ping               v2                                    46b29bc1ff35   3 weeks ago     75.3MB
diamol/ch03-lab        latest                                4658e7be0e41   13 months ago   7.15MB
diamol/ch03-web-ping   latest                                c748a03c9314   19 months ago   75.3MB
moeaidb/aigo           cu10.1-dnn7.6-gpu-darknet-vsc-20.06   f874758ceafc   21 months ago   5.72GB

#解說
REPOSITORY	鏡像的倉庫源
TAG			鏡像的標籤
IMAGE ID	鏡像的ID
CREATED		鏡像的創建時間
SIZE		鏡像的大小

#可選項
 -a, --all			#列出所有鏡像
 -q, --quite		#只顯示鏡像ID
~~~

**docker search 搜索鏡像**

~~~shell
C:\Users\Tai>docker search mysql
NAME                             DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                            MySQL is a widely used, open-source relation…   12383     [OK]
mariadb                          MariaDB Server is a high performing open sou…   4764      [OK]
mysql/mysql-server               Optimized MySQL Server Docker images. Create…   917                  [OK]
percona                          Percona Server is a fork of the MySQL relati…   572       [OK]

#可選項
--filter=STARS=3000		#搜索出來的鏡像就是STARS大於3000
C:\Users\Tai>docker search mysql --filter=STARS=3000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   12383     [OK]
mariadb   MariaDB Server is a high performing open sou…   4764      [OK]

~~~

**docker pull	下載鏡像**

~~~shell
#下載鏡像 docker pull 鏡像名[:tag]
C:\Users\Tai>docker pull mysql
Using default tag: latest	#如果不寫 tag ,默認就是 latest
latest: Pulling from library/mysql
f003217c5aae: Pull complete		#分層下載, docker image的核心, 聯合文件系統
65d94f01a09f: Pull complete
43d78aaa6078: Pull complete
a0f91ffbdf69: Pull complete
59ee9e07e12f: Pull complete
04d82978082c: Pull complete
70f46ebb971a: Pull complete
db6ea71d471d: Pull complete
c2920c795b25: Pull complete
26c3bdf75ff5: Pull complete
9ec1f1f78b0e: Pull complete
4607fa685ac6: Pull complete
Digest: sha256:1c75ba7716c6f73fc106dacedfdcf13f934ea8c161c8b3b3e4618bcd5fbcf195	#簽名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest  #真實地址

#等價於它
docker pull mysql
docker pull docker.io/library/mysql:latest 

#指定版本下載
C:\Users\Tai>docker pull mysql:5.7
5.7: Pulling from library/mysql
f003217c5aae: Already exists
65d94f01a09f: Already exists
43d78aaa6078: Already exists
a0f91ffbdf69: Already exists
59ee9e07e12f: Already exists
04d82978082c: Already exists
70f46ebb971a: Already exists
ba61822c65c2: Pull complete
dec59acdf78a: Pull complete
0a05235a6981: Pull complete
c87d621d6916: Pull complete
Digest: sha256:1a73b6a8f507639a8f91ed01ace28965f4f74bb62a9d9b9e7378d5f07fab79dc
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
~~~

![](https://i.imgur.com/X0aWuPY.png)


**docker rmi 刪除鏡像 !**

~~~shell
C:\Users\Tai>docker rmi -f  容器id	#刪除指定的容器
C:\Users\Tai>docker rmi -f  容器id  容器id  容器id  容器id #刪除多個容器
C:\Users\Tai>docker rmi -f  $(docker images -aq)	#刪除全部容器
~~~

## **容器命令**

**說明:我們有了鏡像才可以創建容器，linux ，下載一個centos鏡像來測試學習**

~~~shell
docker pull centos
~~~

新建容器並啟動

~~~shell
docker run [可選參數] image

#參數說明
--naem="Name"		容器名字  tomcat01  tomcat02, 用來區分容器
-d					後台方式運行
-it					使用交互方式運行，進入容器查看內容
-p					指定容器的端口
	-p ip:主機端口:容器端口
	-p 主機端口:容器端口
	-p容器端口
-p					隨機指定端口

#測試ㄝ, 啟動並進入容器
C:\Users\Tai>docker run -it centos /bin/bash
[root@9ecb051eab23 /]# ls		#查看容器內的centos, 基礎版本, 很多命令都是不完善的
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

#從容器中退出主機
[root@9ecb051eab23 /]# exit

~~~

**列出所有正在運行的容器**

~~~shell
#docker ps 命令
	# 列出當前正在運行的容器
-a	# 列出當前正在運行的容器+帶出歷史運行過得紀錄
-n=?	#顯示最近創建的容器
-q	#只顯示容器編號
C:\Users\Tai>docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

C:\Users\Tai>docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                     PORTS     NAMES
9ecb051eab23   centos    "/bin/bash"   10 minutes ago   Exited (0) 2 minutes ago             eager_bassi

C:\Users\Tai>
~~~

**退出容器**

~~~shell
exit	#直接停止容器
Ctrl + p + q # 容器不停止退出
~~~

**刪除容器**

~~~shell
docker rm 容器id				# 刪除指定容器, 不能刪除正在運行的容器, 如果要強制刪除 rm -f
docker rm -f $(docker ps -aq)  # 刪除所有容器
docker ps -a -q|xargs docker rm # 刪除所有容器
~~~

**啟動和停止容器的操作**

~~~shell
docker start 容器id 		#啟動容器
docker  restart 容器id 	#重啟容器
docker stop 容器id 		#停止當前正在運行的容器
docker kill 容器id 		#強制停止正在當前容器
~~~

## **常用其他命令**

**後臺啟動容器**

~~~shell
# 命令 docker run -d 鏡像名
C:\Users\Tai>docker run -d centos

#問題docker ps, 發現 centos 停止了

#常見的坑, docker 容器使用後台運行,就必須要有前台進程， docker 發現沒有應用，就會自動停止
#nginx, 容器啟動後，發現自己沒有提供服務，就會立刻停止，就是沒有程序了
~~~

**查看日誌**

~~~shell
docker logs -f -t --tail 容器， 沒有日誌

#自己寫一段shell腳本
"while true;do echo kuangshen;sleep 1;done"

#C:\Users\Tai>docker ps
CONTAINER ID   IMAGE    
e3452a05cdca   centos   

#顯示日誌
-tf			  #顯示日誌
--tail number  #顯示日誌條數
C:\Users\Tai>docker logs -tf --tail 10 e3452a05cdca
~~~

**查看容器中的進程訊息**ps

~~~shell
#命令 docker top 容器id
C:\Users\Tai>docker top e3452a05cdca
UID                 PID                 PPID                C                   STIME               TTY             
root                1992                1971                0                   18:06               ?               
root                2407                1992                0                   18:12               ?                 
~~~

**查看鏡像的源數據**

~~~shell
#命令
docker inspect 容器id

#測試
C:\Users\Tai>docker inspect e3452a05cdca
[
    {
        "Id": "e3452a05cdcac5e57180676055b983c40d2faf92259410d00965985821628d67",
        "Created": "2022-04-10T18:06:17.789808133Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo kuangshen;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 1992,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-04-10T18:06:18.252631702Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/e3452a05cdcac5e57180676055b983c40d2faf92259410d00965985821628d67/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/e3452a05cdcac5e57180676055b983c40d2faf92259410d00965985821628d67/hostname",
        "HostsPath": "/var/lib/docker/containers/e3452a05cdcac5e57180676055b983c40d2faf92259410d00965985821628d67/hosts",
        "LogPath": "/var/lib/docker/containers/e3452a05cdcac5e57180676055b983c40d2faf92259410d00965985821628d67/e3452a05cdcac5e57180676055b983c40d2faf92259410d00965985821628d67-json.log",
        "Name": "/intelligent_kalam",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                15,
                129
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/67aa0d7d609f9598faaca05394647a76757f2cba75a238135a12f7b403a34ecc-init/diff:/var/lib/docker/overlay2/568f677a1723c1608a73b6f97aa2a38813623a4e2e5e698db7d7a4a7aff7f88a/diff",
                "MergedDir": "/var/lib/docker/overlay2/67aa0d7d609f9598faaca05394647a76757f2cba75a238135a12f7b403a34ecc/merged",
                "UpperDir": "/var/lib/docker/overlay2/67aa0d7d609f9598faaca05394647a76757f2cba75a238135a12f7b403a34ecc/diff",
                "WorkDir": "/var/lib/docker/overlay2/67aa0d7d609f9598faaca05394647a76757f2cba75a238135a12f7b403a34ecc/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "e3452a05cdca",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo kuangshen;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "c3c96496c9de89b54a09aeca9d328d16dee264b8574d8034345fe8a13dbc774a",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/c3c96496c9de",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "49d63a3cb08f2737854d6b04324fff1be51411bd45b06cbe721d42cd5db40179",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "e2b2f6794ca59913c10984153f79386117f3f5ad35d1c05a481364a8856a6110",
                    "EndpointID": "49d63a3cb08f2737854d6b04324fff1be51411bd45b06cbe721d42cd5db40179",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
~~~

**進入當前正在運行的容器**

~~~shell
#我們通常容器都是使用後台方式運行的，需要進入容器，修改一些配置

#命令
docker exec -it 容器id bashshell

#測試

C:\Users\Tai>docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
e3452a05cdca   centos    "/bin/sh -c 'while t…"   15 minutes ago   Up 15 minutes             intelligent_kalam

C:\Users\Tai>docker exec -it e3452a05cdca
"docker exec" requires at least 2 arguments.
See 'docker exec --help'.

Usage:  docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

Run a command in a running container

C:\Users\Tai>docker exec -it e3452a05cdca /bin/bash
[root@e3452a05cdca /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@e3452a05cdca /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 18:06 ?        00:00:00 /bin/sh -c while true;do echo kuangshen;sleep 1;done
root       956     0  0 18:22 pts/0    00:00:00 /bin/bash
root      1033     1  0 18:23 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
root      1034   956  0 18:23 pts/0    00:00:00 ps -ef
[root@e3452a05cdca /]#

#方式二
docker attach 容器id
#測試
C:\Users\Tai>docker attach e3452a05cdca
正在執行當前代碼...


#docker exec		#進入容器後並凱起一革新的中端，可以在裡面操作(常用)
#docker attach		#進入容器正在執行的終端，不會啟動新的進程
~~~

**從容器內拷貝文件到主機上**

~~~shell
docker cp

~~~

**小結**

![](https://i.imgur.com/OmvHJw1.png)

## **作業練習**

~~~shell
Docker 安裝 Nginx
~~~

~~~shell
#1. 搜索鏡像 search 建議大家去docker搜索ㄝ，可以看到幫助文檔
#2. 下載鏡像 pull
#3. 運行測試
PS C:\Users\Tai\Downloads>  docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        latest    12766a6745ee   12 days ago    142MB
centos       latest    5d0da3dc9764   6 months ago   231MB

# -d 後台運行
# name  給容器命名
# -p 宿主機端口，容器內部端口
PS C:\Users\Tai\Downloads> docker run -d --name nginx01 -p 3344:80 nginx
66241f7cae6ce98f04c27ee8f05444e9787a4c88b5ff29950b06dd233c23accb
PS C:\Users\Tai\Downloads> docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                  NAMES
66241f7cae6c   nginx     "/docker-entrypoint.…"   7 seconds ago   Up 4 seconds   0.0.0.0:3344->80/tcp   nginx01
fa24914a2428   centos    "/bin/bash"              3 hours ago     Up 3 hours
PS C:\Users\Tai\Downloads> curl -v http://localhost:3344
詳細資訊: GET http://localhost:3344/ with 0-byte payload
詳細資訊: received 615-byte response of content type text/html


StatusCode        : 200
StatusDescription : OK
Content           : <!DOCTYPE html>
                    <html>
                    <head>
                    <title>Welcome to nginx!</title>
                    <style>
                    html { color-scheme: light dark; }
                    body { width: 35em; margin: 0 auto;
                    font-family: Tahoma, Verdana, Arial, sans-serif; }
                    </style...
RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Accept-Ranges: bytes
                    Content-Length: 615
                    Content-Type: text/html
                    Date: Mon, 11 Apr 2022 12:29:17 GMT
                    ETag: "61f01158-267"
                    Last-Modified: Tue, 25 Jan 2022 ...
Forms             : {}
Headers           : {[Connection, keep-alive], [Accept-Ranges, bytes], [Content-Length, 615], [Content-Type,
                    text/html]...}
Images            : {}
InputFields       : {}
Links             : {@{innerHTML=nginx.org; innerText=nginx.org; outerHTML=<A href="http://nginx.org/">nginx.org</A>;
                    outerText=nginx.org; tagName=A; href=http://nginx.org/}, @{innerHTML=nginx.com;
                    innerText=nginx.com; outerHTML=<A href="http://nginx.com/">nginx.com</A>; outerText=nginx.com;
                    tagName=A; href=http://nginx.com/}}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 615

#進入容器
PS C:\Users\Tai\Downloads> docker exec -it nginx01 /bin/bash
root@66241f7cae6c:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@66241f7cae6c:/# cd /etc/nginx
root@66241f7cae6c:/etc/nginx# ls
conf.d  fastcgi_params  mime.types  modules  nginx.conf  scgi_params  uwsgi_params
root@66241f7cae6c:/etc/nginx#
~~~

端口暴露的概念

![](https://i.imgur.com/kbUQoN8.png)


思考問題:我們每次改動nginx配置文件，都需要進入容器內部?十分麻煩，我要是可以在容器外部提供一個映射路徑，達到在容器修改文件名，容器內部就可以自動修改? -v 數據卷 !

**作業二:docker 來裝一個tomcat**

~~~shell
#官方的使用
docker run -it --rm tomcat:9.0

#我們之前的起棟都是後台，停止了容器之後，容器還是可以查到  docker run -it --rm, 一般用來測試, 用完就刪除

#下載再啟動
docker pull tomcat

#啟動運行
docker run -d -p 3355:8080 --name tomcat01 tomcat

#測試訪問沒有問題

#進入容器
PS C:\Users\Tai\Downloads> docker exec -it tomcat /bin/bash

#發現問題, 1. linux命令少了, 2. 沒有webapps。 阿里雲鏡像的原因。 默認是最小的鏡像，所以不必要的都替除掉。
#保證最小可以運行
~~~

思考問題: 我們以後要部屬項目, 如果每次都要進入容器是不是十分麻煩?我要是可以在容器提供一個映射路徑, webapps

我們在外部放置項目, 就自動同步到內部就好了 ! 



作業 : 部屬 es + kibana

~~~shell
# es 暴露的端口很多!
# es 十分的耗內存
# es 的數據一般需要放置到安全目錄! 掛載
# --net somenetwork  ?  網路配置

# 啟動
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"
elasticsearch:7.6.2

# 啟動 linux就卡住了  docker stats 查看 cpu 的狀態

# 測試一下es是夠成功了

# es 是十分耗內存的

# 查看 docker stats

#趕緊關閉，增加內存的限制
~~~

![](https://i.imgur.com/e0Ls2pT.png)


~~~shell
#趕緊關閉，增加內存的限制，修改配置文件 -e 環境配置修改
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2

#查看 docker stats
~~~

![](https://i.imgur.com/8HqiIBp.png)


~~~shell
PS C:\Users\Tai> curl http://192.168.1.105:9200


StatusCode        : 200
StatusDescription : OK
Content           : {
                      "name" : "77fa39e35207",
                      "cluster_name" : "docker-cluster",
                      "cluster_uuid" : "lnlU6-DsRJWE-pW4ht8DFA",
                      "version" : {
                        "number" : "7.6.2",
                        "build_flavor" : "default",
                        "build_type" ...
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 541
                    Content-Type: application/json; charset=UTF-8

                    {
                      "name" : "77fa39e35207",
                      "cluster_name" : "docker-cluster",
                      "cluster_uuid" : "lnlU6-DsRJWE-pW4ht8DFA",
                      ...
Forms             : {}
Headers           : {[Content-Length, 541], [Content-Type, application/json; charset=UTF-8]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 541
~~~

作業 : 使用kibana連接es ? 思考網路如果才能連接過去 ! 

![](https://i.imgur.com/DUbqkb6.png)


## **可視化**

1. portainer ( 先用這個 )

~~~shell
#windows
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart always -v \\.\pipe\docker_engine:\\.\pipe\docker_engine -v portainer_data:C:\data portainer/portainer-ce:2.11.1
#linux
docker run -d -p 8088:9000 \ 
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
~~~

2. Rancher ( CI / CD 再用 )

什麼 portainer ? 

Docker 圖形化界面管理工具 ! 提供一個後台面板我們操作 ! 

~~~shell
#windows
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart always -v \\.\pipe\docker_engine:\\.\pipe\docker_engine -v portainer_data:C:\data portainer/portainer-ce:2.11.1
#linux
docker run -d -p 8088:9000 \ 
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
~~~

訪問測試 : https://ip:8088/

通過它來訪問 : 

![](https://i.imgur.com/PpGmoIO.png)


選擇本地

![](https://i.imgur.com/DBI5caB.png)


進入之後的面板

![](https://i.imgur.com/Ios7fgJ.png)


可是化面板我們平時不會使用，自己玩玩即可 ! 

# **Docker 鏡像講解**

## **鏡像是甚麼**

![](https://i.imgur.com/SK2GHhJ.png)


## **Docker鏡像加載原理**

~~~shell
UnionFS(聯合文件系統)
~~~

![](https://i.imgur.com/GGHAjc1.png)


~~~shell
Docker 鏡像加載原理
~~~
![](https://i.imgur.com/u0Du2wj.png)


![](https://i.imgur.com/xCnHrjt.png)

# Dockerfile建置
在Docker的世界裡，有Dockerfile就可以對應一個可以執行的環境(Image)，而Dockerfile就是建置Docker Image的腳本

## 準備dockerfile
下面是一個簡單的Dockerfile，我們預設存在$HOME/project/dockerws/nodejs目錄下
```text=
FROM google/nodejs

# Define working directory.
WORKDIR /data

# Define default command.
EXPOSE 3000
CMD []
ENTRYPOINT ["/nodejs/bin/npm", "start"]
```
- FROM: 指定base image
- WORKDIR: 指定docker執行起來時候的預設目錄位置
- EXPOSE: 指定所有發布的port
- CMD: 指定Instance啟動後所要執行的指令
- ENTRYPOINT: 指令Instance啟動後，程式的進入點

## Build
```shell=
docker build .
```
build動作完成後會回覆一個image id，代表這個image建置完成。

## Build with TAG
一般我們要commit到Docker Hub的話，會需要針對該Docker Image製作一個TAG，之後使用這個TAG做push... 透過"-t"可以指定該build動作到一個TAG上。
```shell=
docker build -t="your-name/image-name" .
```

## Push到Docker Hub
最後透過docker push，即可將該image push到docker hub，供大家使用！
```shell=
docker push your-name/image-name
```