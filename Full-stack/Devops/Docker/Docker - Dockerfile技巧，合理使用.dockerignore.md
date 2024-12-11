---
Date: 2024-06-01
tags:
  - docker
  - devops
---
### .Dockerignore
Docker是 Client - Server 架構，理論上 Client 和 Server 可以不在一台機器上。

在建構docker映像的時候，需要把所需的檔案由CLI（Client）發給Server，這些檔案其實就是在build context.
舉例：
```shell
$ dockerfile-demo more Dockerfile
FROM python:3.9.5-slim

RUN pip install flask

WORKDIR /src
ENV FLASK_APP=app.py

COPY app.py /src/app.py

EXPOSE 5000

CMD ["flask", "run", "-h", "0.0.0.0"]

$ dockerfile-demo more app.py
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, world!'
```

建置的時候，第一行輸出就是發送建置上下文。
**`.`這個參數就是代表了build context所指向的目錄**

```shell
$ docker image build -t demo .
Sending build context to Docker daemon  11.13MB
Step 1/7 : FROM python:3.9.5-slim
 ---> 609da079b03a
Step 2/7 : RUN pip install flask
 ---> Using cache
 ---> 955ce495635e
Step 3/7 : WORKDIR /src
 ---> Using cache
 ---> 1c2f968e9f9b
Step 4/7 : ENV FLASK_APP=app.py
 ---> Using cache
 ---> dceb15b338cf
Step 5/7 : COPY app.py /src/app.py
 ---> Using cache
 ---> 0d4dfef28b5f
Step 6/7 : EXPOSE 5000
 ---> Using cache
 ---> 203e9865f0d9
Step 7/7 : CMD ["flask", "run", "-h", "0.0.0.0"]
 ---> Using cache
 ---> 35b5efae1293
Successfully built 35b5efae1293
Successfully tagged demo:latest
```

所以我們可以使用`.dockerignore`來進行限制，或是說你在建置時忽略那些文件，跟`.gitignore`使用方式一樣。

.dockerignore 檔案
```dockerignore
.vscode/
env/
```
有了.dockerignore檔案後，我們再build，build context就小了很多，4.096kB
```shell
$ docker image build -t demo .
Sending build context to Docker daemon  4.096kB
Step 1/7 : FROM python:3.9.5-slim
---> 609da079b03a
Step 2/7 : RUN pip install flask
---> Using cache
---> 955ce495635e
Step 3/7 : WORKDIR /src
---> Using cache
---> 1c2f968e9f9b
Step 4/7 : ENV FLASK_APP=app.py
---> Using cache
---> dceb15b338cf
Step 5/7 : COPY . /src/
---> a9a8f888fef3
Step 6/7 : EXPOSE 5000
---> Running in c71f34d32009
Removing intermediate container c71f34d32009
---> fed6995d5a83
Step 7/7 : CMD ["flask", "run", "-h", "0.0.0.0"]
---> Running in 7ea669f59d5e
Removing intermediate container 7ea669f59d5e
---> 079bae887a47
Successfully built 079bae887a47
Successfully tagged demo:latest
```

```
## Expose
這邊的曝露端口，如果不在 dockerfile 寫 ，其實我們可以使用 `-p` 來指定轉發的端口，那 `expose` 主要作用是什麼呢，如果當我們要把這個鏡像給其他人使用時，可以給對方確認到 端口位址，類似於文檔的這個效果。

```shell
docker image inspect <image-name>
```
>[!info]
>我們會發現在 config 就會多一個 曝露的端口號。
