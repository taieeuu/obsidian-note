---
Date: 2024-06-04
tags:
  - docker
  - devops
---
## Podman 中 Pod 基本操作
創建一個 Pod
```shell
podman pod create --name demo
```

查看 Pod 的整體資訊
```shell
podman pod ps
```
>[!info]
>列出所有的 Pod

查看每個容器的詳細資訊及其所屬的 Pod
```shell
podman ps --pod
```
>[!info]
>列出所有正在運行的容器，並附帶顯示每個容器所屬的 Pod 的相關資訊。與 `podman pod ps` 不同，這個指令主要針對的是容器，而不是 Pod。
>加上`-a`，列出所有容器（包括正在運行和已停止的容器）

假設我們有一個 pod 跟一個 image ，我們把它 run 起來並指定 pod 給他

```shell
podman container run -d --name test1 --pod demo docker.io/library/busybox ping 8.8.8.8
```
>[!info]
>這個指令用來創建並在後台運行一個新的容器
>- `podman container run`：創建並運行一個新的容器。
>- `-d` 或 `--detach`：使容器在後台運行。
>- `--name test1`：為新容器指定名稱為 `test1`。
>- `--pod demo`：將新容器添加到名為 `demo` 的 Pod 中。
>- `docker.io/library/busybox`：指定要使用的映像檔，這裡使用的是來自 Docker Hub 的 `busybox` 映像檔。
>- `ping 8.8.8.8`：容器運行的指令，即在容器內執行 `ping` 指令來連接 `8.8.8.8`（Google 的公共 DNS 伺服器）。

再創建一個一樣的容器命名為 test2

然後我們進入兩個容器看看
```shell
podman container exec -it test1 sh

ip a
```
>[!info]
>進入容器使用交互模式，且打印出 所有的網路卡位址。

我們透過 `ip a` 這個指令查看 ip地址時會發現，他們的 tap0 接口的 ip地址是一樣的，也就是說**處在同一個Pod中的容器他們具有相同的IP地址**

因為一個 Pod 就是一個命名空間，在這個命名空間裡的容器他們處在同一個網絡命名空間裡面，所以不管我們有多少個容器，只要他處在相同的 Pod 他們都有相同的 IP地址。

那這樣就有一個優勢，在相同的命名空間就表示，我們可以通過本地的IP地址進行通信的。

他跟我們的 docker-compose其實有雷同處，docker-compose 可以佈署多容器的應用，那 podman 可以透過 pod 來佈署多容器的應用，只不過我們在 podman 不能使用我們 docker-compose的yml文件，但是 podman 裡面有一個 `play` 的命令，可以透過這個命令來執行 k8s.yml 文件，裡面也設定有關容器等操作。