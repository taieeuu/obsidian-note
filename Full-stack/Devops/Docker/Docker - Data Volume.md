---
Date: 2024-06-09
tags:
  - docker
  - devops
---
## Docker Volume
### 環境準備
準備一個Dockerfile和一個my-cron的文件
```shell
$ ls
Dockerfile  my-cron
$ more Dockerfile
FROM alpine:latest
RUN apk update
RUN apk --no-cache add curl
ENV SUPERCRONIC_URL=https://github.com/aptible/supercronic/releases/download/v0.1.12/supercronic-linux-amd64 \
    SUPERCRONIC=supercronic-linux-amd64 \
    SUPERCRONIC_SHA1SUM=048b95b48b708983effb2e5c935a1ef8483d9e3e
RUN curl -fsSLO "$SUPERCRONIC_URL" \
    && echo "${SUPERCRONIC_SHA1SUM}  ${SUPERCRONIC}" | sha1sum -c - \
    && chmod +x "$SUPERCRONIC" \
    && mv "$SUPERCRONIC" "/usr/local/bin/${SUPERCRONIC}" \
    && ln -s "/usr/local/bin/${SUPERCRONIC}" /usr/local/bin/supercronic
COPY my-cron /app/my-cron
WORKDIR /app

VOLUME ["/app"]

# RUN cron job
CMD ["/usr/local/bin/supercronic", "/app/my-cron"]
$
$ more my-cron
*/1 * * * * date >> /app/test.txt
```
### 建構鏡像
```shell
$ docker image build -t my-cron .
$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
my-cron      latest    e9fbd9a562c9   4 seconds ago   24.7MB
```
### 建立容器(不指定-v參數)
此時Docker會自動建立一個隨機名稱的捲，將我們儲存在Dockerfile定義的磁碟區中`VOLUME ["/app"]`
```shell
$ docker run -d my-cron
9a8fa93f03c42427a498b21ac520660752122e20bcdbf939661646f71d277f8f
$ docker volume ls
DRIVER    VOLUME NAME
local     043a196c21202c484c69f2098b6b9ec22b9a9e4e4bb8d4f55a4c3dce13c15264
$ docker volume inspect 043a196c21202c484c69f2098b6b9ec22b9a9e4e4bb8d4f55a4c3dce13c15264
[
    {
        "CreatedAt": "2021-06-22T23:06:13+02:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/043a196c21202c484c69f2098b6b9ec22b9a9e4e4bb8d4f55a4c3dce13c15264/_data",
        "Name": "043a196c21202c484c69f2098b6b9ec22b9a9e4e4bb8d4f55a4c3dce13c15264",
        "Options": null,
        "Scope": "local"
    }
]
```
>[!info]
>在這個磁碟區的掛載點可以找到容器所建立的文件

### 建立容器(指定-v參數)
在建立容器的時候透過`-v`參數我們可以手動的指定需要建立Volume的名字，以及對應容器內的路徑，這個路徑是可以任意的，不一定需要在Dockerfile裡透過VOLUME定義

比如說我們把上面的Dockerfile裡的VOLUME刪除
```shell
FROM alpine:latest
RUN apk update
RUN apk --no-cache add curl
ENV SUPERCRONIC_URL=https://github.com/aptible/supercronic/releases/download/v0.1.12/supercronic-linux-amd64 \
    SUPERCRONIC=supercronic-linux-amd64 \
    SUPERCRONIC_SHA1SUM=048b95b48b708983effb2e5c935a1ef8483d9e3e
RUN curl -fsSLO "$SUPERCRONIC_URL" \
    && echo "${SUPERCRONIC_SHA1SUM}  ${SUPERCRONIC}" | sha1sum -c - \
    && chmod +x "$SUPERCRONIC" \
    && mv "$SUPERCRONIC" "/usr/local/bin/${SUPERCRONIC}" \
    && ln -s "/usr/local/bin/${SUPERCRONIC}" /usr/local/bin/supercronic
COPY my-cron /app/my-cron
WORKDIR /app

# RUN cron job
CMD ["/usr/local/bin/supercronic", "/app/my-cron"]
```

重新建構鏡像，然後建立容器，新增-v參數

```shell
$ docker image build -t my-cron .
$ docker container run -d -v cron-data:/app my-cron
43c6d0357b0893861092a752c61ab01bdfa62ea766d01d2fcb8b3ecb6c88b3de
$ docker volume ls
DRIVER    VOLUME NAME
local     cron-data
$ docker volume inspect cron-data
[
    {
        "CreatedAt": "2021-06-22T23:25:02+02:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/cron-data/_data",
        "Name": "cron-data",
        "Options": null,
        "Scope": "local"
    }
]
$ ls /var/lib/docker/volumes/cron-data/_data
my-cron
$ ls /var/lib/docker/volumes/cron-data/_data
my-cron  test.txt
```

體積也創建了。
### 環境清理
強制刪除所有容器、系統清理和流量清理
```shell
$ docker rm -f $(docker container ps -aq)
$ docker system prune -f
$ docker volume prune -f
```
