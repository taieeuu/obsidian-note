---
Date: 2024-06-02
tags:
  - docker
  - devops
---
## Python Flask + Redis 練習
Python
```python
from flask import Flask
from redis import Redis
import os
import socket

app = Flask(__name__)
redis = Redis(host=os.environ.get('REDIS_HOST', '127.0.0.1'), port=6379)


@app.route('/')
def hello():
    redis.incr('hits')
    return f"Hello Container World! I have been seen {redis.get('hits').decode('utf-8')} times and my hostname is {socket.gethostname()}.\n"
```

Dockerfile
```dockerfile
FROM python:3.9.5-slim

RUN pip install flask redis && \
    groupadd -r flask && useradd -r -g flask flask && \
    mkdir /src && \
    chown -R flask:flask /src

USER flask

COPY app.py /src/app.py

WORKDIR /src

ENV FLASK_APP=app.py REDIS_HOST=redis

EXPOSE 5000

CMD ["flask", "run", "-h", "0.0.0.0"]
```

![[Pasted image 20240602120321.png]]

>[!info]
>我們會有兩個 Container 要去讓他們之間進行通信，然後我們一般都會使用自己創建的network。

#### 建構flask鏡像，準備一個redis鏡像。
```shell
$ docker image pull redis
$ docker image build -t flask-demo .
$ docker image ls
REPOSITORY   TAG          IMAGE ID       CREATED              SIZE
flask-demo   latest       4778411a24c5   About a minute ago   126MB
python       3.9.5-slim   c71955050276   8 days ago           115MB
redis        latest       08502081bff6   2 weeks ago          105MB
```
#### 建立一個docker Bridge
```shell
$ docker network create -d bridge demo-network
8005f4348c44ffe3cdcbbda165beea2b0cb520179d3745b24e8f9e05a3e6456d
$ docker network ls
NETWORK ID     NAME           DRIVER    SCOPE
2a464c0b8ec7   bridge         bridge    local
8005f4348c44   demo-network   bridge    local
80b63f711a37   host           host      local
fae746a75be1   none           null      local
$
```
#### 建立redis容器
建立一個叫`redis-server`的容器，連接到 demo-network 上
```shell
$ docker container run -d --name redis-server --network demo-network redis
002800c265020310231d689e6fd35bc084a0fa015e8b0a3174aa2c5e29824c0e
$ docker container ls
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS      NAMES
002800c26502   redis     "docker-entrypoint.s…"   4 seconds ago   Up 3 seconds   6379/tcp   redis-server
$
```
#### 建立flask容器
```shell
$ docker container run -d --network demo-network --name flask-demo --env REDIS_HOST=redis-server -p 5000:5000 flask-demo
```
>[!info]
>瀏覽器造訪[http://127.0.0.1:5000](http://127.0.0.1:5000/)
>應該可以看到類似下面的內容，每次刷新頁面，統計加1
>你好，貨櫃世界！我已被看到 36 次，我的主機名稱是 925ecb8d111a。
#### ## 總結
如果將上面的混音在一起，成為一個設定 Shell 腳本步驟
```shell
# prepare image
docker image pull redis
docker image build -t flask-demo .

# create network
docker network create -d bridge demo-network

# create container
docker container run -d --name redis-server --network demo-network redis
docker container run -d --network demo-network --name flask-demo --env REDIS_HOST=redis-server -p 5000:5000 flask-demo
```

上述的例子是很常有這種的情況，創建自己網路不同容器。保證他們之間連接等，所以 docker 就有提供一個工具，叫 docker-compose ，透過 docker-compose 就會非常方便進行這樣子的佈署，可以寫一個配置文件，去定義容器以及他們之間得訪問關係，透過一條命令把它搭建起來。