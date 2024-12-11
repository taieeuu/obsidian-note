---
Date: 2024-06-01
tags:
  - docker
  - devops
---
>[!Info]
>如果還是有問題或是想了解更詳細內容，請參閱[官方文檔](https://docs.docker.com/reference/dockerfile/)
## 基本原則
- 官方映像優於非官方的映像，如果沒有官方鏡像，則盡量選擇Dockerfile開源的
- 固定版本tag而不是每次都使用latest
- 盡量選擇體積小的鏡像，像是 alpine、slim等
## RUN 執行指令
`RUN` 主要用於在Image裡執行指令，例如安裝軟體，下載檔案等。
```cmd
$ apt-get update
$ apt-get install wget
$ wget https://github.com/ipinfo/cli/releases/download/ipinfo-2.0.1/ipinfo_2.0.1_linux_amd64.tar.gz
$ tar zxf ipinfo_2.0.1_linux_amd64.tar.gz
$ mv ipinfo_2.0.1_linux_amd64 /usr/bin/ipinfo
$ rm -rf ipinfo_2.0.1_linux_amd64.tar.gz
```

在Dockerfile我們可以這樣寫
```dockerfile
FROM ubuntu:20.04
RUN apt-get update
RUN apt-get install -y wget
RUN wget https://github.com/ipinfo/cli/releases/download/ipinfo-2.0.1/ipinfo_2.0.1_linux_amd64.tar.gz
RUN tar zxf ipinfo_2.0.1_linux_amd64.tar.gz
RUN mv ipinfo_2.0.1_linux_amd64 /usr/bin/ipinfo
RUN rm -rf ipinfo_2.0.1_linux_amd64.tar.gz
```
**注意** 我們不太建議分這麼多次RUN去做這件事情，你可以看到上方`RUN`運行了六次，也就代表他有六層，docker image的分層，這樣會導致image變成非常大，所以要盡量放進同一個`RUN`命令中。

以下是改進版本
```dockerfile
FROM ubuntu:20.04
RUN apt-get update && \
    apt-get install -y wget && \
    wget https://github.com/ipinfo/cli/releases/download/ipinfo-2.0.1/ipinfo_2.0.1_linux_amd64.tar.gz && \
    tar zxf ipinfo_2.0.1_linux_amd64.tar.gz && \
    mv ipinfo_2.0.1_linux_amd64 /usr/bin/ipinfo && \
    rm -rf ipinfo_2.0.1_linux_amd64.tar.gz
```
## 文件複製和目錄操作 (ADD,COPY,WORKDIR)
### 複製普通文件
`Copy`和`Add`都可以把local的一個文件複製到鏡像裡，如果目標目錄不存在則會自動創建
```dockerfile
FROM python:3.9.5-alpine3.13
COPY hello.py /app/hello.py
```
### 複製壓縮文件
`Add`比 `Copy`高級一點的地方就是，如果複製的是一個gzip等壓縮檔時，`Add`會幫助我們自動去解壓縮檔。
```dockerfile
FROM python:3.9.5-alpine3.13
ADD hello.tar.gz /app/
```
### 切換目錄
```dockerfile
FROM python:3.9.5-alpine3.13
Workdir /app
COPY . . #他會複製到/APP下，因為workdir指令
```
## 建構參數和環境變數 (ARG vs ENV)
`ARG` 和 `ENV` 是經常容易被混淆的兩個Dockerfile的語法，都可以用來設定一個「變數」。 但實際上兩者有很多的不同。
### ENV
```dockerfile
FROM ubuntu:20.04
ENV VERSION=2.0.1
RUN apt-get update && \
    apt-get install -y wget && \
    wget https://github.com/ipinfo/cli/releases/download/ipinfo-${VERSION}/ipinfo_${VERSION}_linux_amd64.tar.gz && \
    tar zxf ipinfo_${VERSION}_linux_amd64.tar.gz && \
    mv ipinfo_${VERSION}_linux_amd64 /usr/bin/ipinfo && \
    rm -rf ipinfo_${VERSION}_linux_amd64.tar.gz
```
### ARG
```dockerfile
FROM ubuntu:20.04
ARG VERSION=2.0.1
RUN apt-get update && \
    apt-get install -y wget && \
    wget https://github.com/ipinfo/cli/releases/download/ipinfo-${VERSION}/ipinfo_${VERSION}_linux_amd64.tar.gz && \
    tar zxf ipinfo_${VERSION}_linux_amd64.tar.gz && \
    mv ipinfo_${VERSION}_linux_amd64 /usr/bin/ipinfo && \
    rm -rf ipinfo_${VERSION}_linux_amd64.tar.gz
```
### 區別
![[Pasted image 20240601165923.png|275]]

ARG 可以在鏡像build的時候動態修改value, 通過 `--build-arg`
ENV 設定的變數可以在Image中保持，並在容器中的環境變數裡
```shell
$ docker image build -f .\Dockerfile-arg -t ipinfo-arg-2.0.0 --build-arg VERSION=2.0.0 .
$ docker image ls
REPOSITORY         TAG       IMAGE ID       CREATED          SIZE
ipinfo-arg-2.0.0   latest    0d9c964947e2   6 seconds ago    124MB
$ docker container run -it ipinfo-arg-2.0.0
root@b64285579756:/#
root@b64285579756:/# ipinfo version
2.0.0
root@b64285579756:/#
```
## 容器啟動命令 CMD
CMD可以用來設定容器啟動時預設會執行的指令。
- 容器啟動時預設執行的命令
- 如果docker container run啟動容器時指定了其它命令，則CMD命令會被忽略
- 如果定義了多個CMD，只有最後一個會被執行。

舉例:
我們查看該 image 的歷史資料可以發現，在構築我們的dockerfile命令前，就已經有了`/bin/sh`這個 CMD 指令，如果我們在dockerfile在有一個CMD指令的話則會覆蓋她。
![[Pasted image 20240601200405.png]]
## 容器啟動指令 ENTRYPOINT
ENTRYPOINT 也可以設定容器啟動時要執行的指令，但是和CMD是有差別的。
- CMD 設定的指令，可以在docker container run 時傳入其它指令，覆寫 CMD 的指令，但是 ENTRYPOINT 設定的指令是一定會被執行的。
- ENTRYPOINT 和 CMD 可以聯合使用，ENTRYPOINT 設定執行的指令，CMD傳遞參數

下面的Dockerfile build成一個叫 demo-cmd 的鏡像
```dockerfile
FROM ubuntu:20.04
CMD ["echo", "hello docker"]
```

下面 build 成一個叫 demo-entrypoint 的鏡像
```dockerfile
FROM ubuntu:20.04
ENTRYPOINT ["echo", "hello docker"]
```

CMD的映像，如果執行建立容器，不指定執行時的指令，則會預設執行CMD所定義的指令，列印出hello docker

```shell
$ docker container run -it --rm demo-cmd
hello docker
```

但是如果我們docker container run的時候指定命令，則該命令會覆寫CMD的命令，如：
```shell
$ docker container run -it --rm demo-cmd echo "hello world"
hello world
```

但是ENTRYPOINT的容器裡ENTRYPOINT所定義的指令則無法覆蓋，一定會執行
```shell
$ docker container run -it --rm demo-entrypoint
hello docker
$ docker container run -it --rm demo-entrypoint echo "hello world"
hello docker echo hello world
```
## Shell 格式和 Exec 格式
`CMD`和`ENTRYPOINT`同時支援shell格式和Exec格式。
### Shell
```dockerfile
CMD echo "hello docker"
```

```shell
ENTRYPOINT echo "hello docker"
```
### Exec格式
```dockerfile
ENTRYPOINT ["echo", "hello docker"]
```

```shell
CMD ["echo", "hello docker"]
```

**注意shell腳本的問題**
```dockerfile
FROM ubuntu:20.04
ENV NAME=docker
CMD echo "hello $NAME"
```
假如我們要把上面的CMD改成Exec格式，下面這樣改是不行的, 大家可以試試看。
```dockerfile
FROM ubuntu:20.04
ENV NAME=docker
CMD ["echo", "hello $NAME"]
```
它會印出, 而不是,那麼需要怎麼寫呢？ 我們需要以shell腳本的方式執行`hello $NAME``hello docker`
```dockerfile
FROM ubuntu:20.04
ENV NAME=docker
CMD ["sh", "-c", "echo hello $NAME"]
```

## Container Health

我們使用下面的 flask進行測試
```python
from flask import Flask
from redis import StrictRedis
import os
import socket

app = Flask(__name__)
redis = StrictRedis(host=os.environ.get('REDIS_HOST', '127.0.0.1'),
                    port=6379, password=os.environ.get('REDIS_PASS'))

@app.route('/')
def hello():
    redis.incr('hits')
    return f"Hello Container World! I have been seen {redis.get('hits').decode('utf-8')} times and my hostname is {socket.gethostname()}.\n"
```

下面的 Dockerfile 裡的 HEALTHCHECK 就定義了一個健康檢查。
```dockerfile
FROM python:3.9.5-slim

RUN pip install flask redis && \
    apt-get update && \
    apt-get install -y curl && \
    groupadd -r flask && useradd -r -g flask flask && \
    mkdir /src && \
    chown -R flask:flask /src

USER flask

COPY app.py /src/app.py

WORKDIR /src

ENV FLASK_APP=app.py REDIS_HOST=redis

EXPOSE 5000

HEALTHCHECK --interval=30s --timeout=3s \
    CMD curl -f http://localhost:5000/ || exit 1

CMD ["flask", "run", "-h", "0.0.0.0"]
```

建立鏡像，建立一個橋接網絡，然後啟動容器連接到橋接網絡
```shell
$ docker image build -t flask-demo .
$ docker network create mybridge
$ docker container run -d --network mybridge --env REDIS_PASS=abc123 flask-demo
```

可以透過 `docker container ls`查看健康度
```shell
$ docker container ls
CONTAINER ID   IMAGE        COMMAND                  CREATED       STATUS                            PORTS      NAMES
059c12486019   flask-demo   "flask run -h 0.0.0.0"   4 hours ago   Up 8 seconds (health: starting)   5000/tcp   dazzling_tereshkova
```

經過3次檢查，一直是不通的，然後健康的狀態就會從開始變成不健康
```shell
docker container ls
CONTAINER ID   IMAGE        COMMAND                  CREATED       STATUS                     PORTS      NAMES
059c12486019   flask-demo   "flask run -h 0.0.0.0"   4 hours ago   Up 2 minutes (unhealthy)   5000/tcp   dazzling_tereshkova
```
### 啟動redis伺服器
啟動redis，連到mybridge上，name=redis，注意密碼
```shell
$ docker container run -d --network mybridge --name redis redis:latest redis-server --requirepass abc123
```

經過幾次衛生，我們的燒瓶變得健康了
```shell
$ docker container ls
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS                   PORTS      NAMES
bc4e826ee938   redis:latest   "docker-entrypoint.s…"   18 seconds ago   Up 16 seconds            6379/tcp   redis
059c12486019   flask-demo     "flask run -h 0.0.0.0"   4 hours ago      Up 6 minutes (healthy)   5000/tcp   dazzling_tereshkova
```