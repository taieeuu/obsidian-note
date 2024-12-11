---
Date: 2024-06-01
tags:
  - docker
  - devops
---
## 學習使用非 root 使用者
### Root的危險性
docker的root權限一直是其受詬病的地方，docker的root權限有那麼危險嗎？

假設我們有一個用戶，叫demo，它本身不具備sudo的權限，所以遇到很多檔案無法進行讀寫操作，例如/root目錄是無法查看的。

```shell
[demo@docker-host ~]$ sudo ls /root
[sudo] password for demo:
demo is not in the sudoers file.  This incident will be reported.
[demo@docker-host ~]$
```

但是這個使用者擁有執行docker的權限，所以它在docker這個群組裡。

```shell
[demo@docker-host ~]$ groups
demo docker
[demo@docker-host ~]$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
busybox      latest    a9d583973f65   2 days ago   1.23MB
[demo@docker-host ~]$
```

這時候，我們就可以透過Docker做很多越權的事情了，例如，我們可以把這個無法查看的/root目錄映射到docker容器裡，你就可以自由的進行查看了。

```shell
[demo@docker-host vagrant]$ docker run -it -v /root/:/root/tmp busybox sh
/ # cd /root/tmp
~/tmp # ls
anaconda-ks.cfg  original-ks.cfg
~/tmp # ls -l
total 16
-rw-------    1 root     root          5570 Apr 30  2020 anaconda-ks.cfg
-rw-------    1 root     root          5300 Apr 30  2020 original-ks.cfg
~/tmp #
```

甚至我們可以給我們自己加sudo權限。

```shell
[demo@docker-host ~]$ sudo vim /etc/sudoers
[sudo] password for demo:
demo is not in the sudoers file.  This incident will be reported.
[demo@docker-host ~]$
```

然後退出容器，我們有sudo權限了。

```shell
[demo@docker-host ~]$ sudo more /etc/sudoers | grep demo
demo    ALL=(ALL)       ALL
[demo@docker-host ~]$
```
### 如何使用非root用戶
Dockerfile
```dockerfile
FROM python:3.9.5-slim

RUN pip install flask

COPY app.py /src/app.py

WORKDIR /src
ENV FLASK_APP=app.py

EXPOSE 5000

CMD ["flask", "run", "-h", "0.0.0.0"]
```

假設建構的鏡像名稱為`flask-demo`

第二個Dockerfile，使用非root使用者來建構這個映像，名字叫`flask-no-root`Dockerfile如下：
- 透過groupadd和useradd建立一個flask的群組和用戶
- 透過USER指定後面的指令要以flask這個使用者的身分來執行

```dockerfile
FROM python:3.9.5-slim

RUN pip install flask && \
    groupadd -r flask && useradd -r -g flask flask && \
    mkdir /src && \
    chown -R flask:flask /src

USER flask

COPY app.py /src/app.py

WORKDIR /src
ENV FLASK_APP=app.py

EXPOSE 5000

CMD ["flask", "run", "-h", "0.0.0.0"]
```

```shell
$ docker image ls
REPOSITORY      TAG          IMAGE ID       CREATED          SIZE
flask-no-root   latest       80996843356e   41 minutes ago   126MB
flask-demo      latest       2696c68b51ce   49 minutes ago   125MB
python          3.9.5-slim   609da079b03a   2 weeks ago      115MB
```

分別使用這兩個鏡像來建立兩個容器

```shell
$ docker run -d --name flask-root flask-demo
b31588bae216951e7981ce14290d74d377eef477f71e1506b17ee505d7994774
$ docker run -d --name flask-no-root flask-no-root
83aaa4a116608ec98afff2a142392119b7efe53617db213e8c7276ab0ae0aaa0
$ docker container ps
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS      NAMES
83aaa4a11660   flask-no-root   "flask run -h 0.0.0.0"   4 seconds ago    Up 3 seconds    5000/tcp   flask-no-root
b31588bae216   flask-demo      "flask run -h 0.0.0.0"   16 seconds ago   Up 15 seconds   5000/tcp   flask-root
