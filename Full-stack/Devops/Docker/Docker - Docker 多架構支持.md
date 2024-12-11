---
Date: 2024-06-08
tags:
  - docker
  - devops
---
## Why ?
當我們在使用某一個鏡像構建一個容器時候，並不會因為作業系統不同而需要使用不同的指令，那是因為 Docker 會自動幫我們選擇。
![[Docker 多架構支持 1.png]]

可以看到下面圖，這是一個官方的image，我們可以發現基本上都會提供多架構的版本。
![[Pasted image 20240608141258.png]]

我們可以使用以下命令，找到`Architecture`就會看到自己目前的架構是甚麼了。
```shell
docker image inspect <id>
```
## 使用 buildx 建構多架構鏡像
Windows和Mac的桌面版Docker自備buildx指令，但Linux環境下的Docker需要自行安裝buildx

原始碼[https://github.com/xiaopeng163/flask-redis](https://github.com/xiaopeng163/flask-redis)

登入dockerhub
```shell
docker login
```

進入目錄（Dockerfile所在目錄原始碼）
```shell
git clone https://github.com/xiaopeng163/flask-redis
cd flask-redis
```

建構
```shell
PS C:\Users\Peng Xiao\flask-redis> docker login
Authenticating with existing credentials...
Login Succeeded
PS C:\Users\Peng Xiao\flask-redis> docker buildx build --push --platform linux/arm/v7,linux/arm64/v8,linux/amd64 -t xiaopeng163/flask-redis:latest .
[+] Building 0.0s (0/0)
error: multiple platforms feature is currently not supported for docker driver. Please switch to a different driver (eg. "docker buildx create --use")
PS C:\Users\Peng Xiao\flask-redis>
PS C:\Users\Peng Xiao\flask-redis> docker buildx ls
NAME/NODE       DRIVER/ENDPOINT STATUS  PLATFORMS
desktop-linux   docker
desktop-linux desktop-linux   running linux/amd64, linux/arm64, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/arm/v7, linux/arm/v6
default *       docker
default       default         running linux/amd64, linux/arm64, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/arm/v7, linux/arm/v6
PS C:\Users\Peng Xiao\flask-redis> docker buildx create --name mybuilder --use
mybuilder
PS C:\Users\Peng Xiao\flask-redis> docker buildx ls
NAME/NODE       DRIVER/ENDPOINT                STATUS   PLATFORMS
mybuilder *     docker-container
mybuilder0    npipe:////./pipe/docker_engine inactive
desktop-linux   docker
desktop-linux desktop-linux                  running  linux/amd64, linux/arm64, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/arm/v7, linux/arm/v6
default         docker
default       default                        running  linux/amd64, linux/arm64, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/arm/v7, linux/arm/v6
PS C:\Users\Peng Xiao\flask-redis> docker buildx build --push --platform linux/arm/v7,linux/arm64/v8,linux/amd64 -t xiaopeng163/flask-redis:latest .
```
>[!info]
>1. **`docker buildx build`**：
>	- 這是使用 Buildx 插件來構建 Docker 映像的命令。Buildx 支持構建多架構映像，可以同時為多個硬體架構生成映像。
>2. **`--push`**：
>	- 構建完成後，自動將生成的映像推送到指定的 Docker Registry（例如 Docker Hub）。
>3. **`--platform linux/arm/v7,linux/arm64/v8,linux/amd64`**：
>	- 指定需要構建的映像支援的平台和架構。
>		- `linux/arm/v7`：針對 ARMv7 架構（32 位元）的映像。
>		- `linux/arm64/v8`：針對 ARM64 架構（64 位元）的映像。
>		- `linux/amd64`：針對 x86_64 架構（64 位元）的映像。
>4. **`-t xiaopeng163/flask-redis:latest`**：
>	- 為構建的映像指定標籤。
>	- `xiaopeng163`：這是你的 Docker Hub 使用者名稱或其他 Registry 的命名空間。
>	- `flask-redis`：映像名稱，這裡表示這是一個包含 Flask 和 Redis 的應用映像。
>	- `:latest`：標籤，表示這是最新版本的映像。
>5. **`.`**：
>	- 上下文路徑，表示 Dockerfile 和構建上下文的所在位置，這裡是當前目錄。
### 流程
這條命令的工作流程如下：
1. Docker Buildx 將根據當前目錄下的 Dockerfile 和構建上下文來構建映像。
2. 根據指定的平台（ARMv7、ARM64 和 AMD64），Buildx 會為每個平台構建對應的映像。
3. 在構建完成後，Buildx 將所有這些映像推送到 Docker Hub，並標籤為 `xiaopeng163/flask-redis:latest`。
4. 最終，Docker Hub 上會有一個多架構映像，當用戶從不同架構的設備上拉取這個映像時，Docker 會自動選擇合適的映像變體。

你可以從以下命令看到構建器的容器運行
```shell
docker container ls
```

也就是剛剛所查看 構建器 
```shell
docker buildx ls
```

構建完，我們可以把它刪除
```shell
docker container rm -f <id>

docker buildx rm <buildx-name>
```

