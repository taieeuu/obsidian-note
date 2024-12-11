---
Date: 2024-06-09
tags:
  - docker
  - devops
---
## Bind Mount
```shell
docker container run -d -v ${pwd}:/app my-cron
```
>[!info]
>- `docker container run`：這是用來運行Docker容器的指令。
>- `-d`：這個選項表示容器將在後台運行（detached mode）。
>- `-v ${pwd}:/app`：這個選項用來將主機的當前目錄（`${pwd}`）掛載到容器內的 `/app` 目錄。
>- `my-cron`：這是要運行的容器映像名稱。

在使用 Bind Mount 之前要把需要 Mount 的目錄，添加至 Docker Desktop中的 Resources 目錄中。