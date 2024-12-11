---
Date: 2024-06-02
tags:
  - docker
  - devops
---
`docker-compose`是一個用於定義和運行多容器Docker 應用程式的工具。透過一個設定檔（通常命名為`docker-compose.yml`），你可以設定你的應用程式服務、網路、磁碟區等各種資源。這裡是一些基本的`docker-compose`命令，它們用於管理和操作由`docker-compose.yml`檔案定義的應用：

## 常用的指令
**啟動服務**
```shell
docker-compose up
```
>[!info]
>1. 這個指令會根據`docker-compose.yml`文件的配置，啟動所有服務。加上`-d`參數可以讓服務在背景運行（detached mode）。
>2. 或是使用 `-p` 指定項目名稱，在Docker Compose 中，專案名稱用於建立網路、容器和其他資源的命名前綴。如果不指定專案名稱，Docker Compose 預設使用目前目錄名稱作為專案名稱。**但要注意一旦使用則往後都需要使用，不然docker-compse無法察覺他**

**停止服務**
```shell
docker-compose down
```
>[!info]
>停止並移除`docker-compose up`所有由 指令啟動的容器。可以新增`--volumes`來同時移除資料卷。

**查看服務狀態**
```shell
docker-compose ps
```
>[!info]
>顯示目前目錄的`docker-compose.yml`檔案定義的所有服務的狀態。

**查看服務的日誌**
```shell
docker-compose logs
```
>[!info]
>顯示一個或多個服務的日誌輸出。加上`-f`參數可以持續追蹤日誌輸出。

**建構服務**
```shell
docker-compose build
```
>[!info]
>建置服務容器。通常用於需要建立鏡像的服務，`docker-compose.yml`檔案中有build 指令的服務。

**重啟服務**
```shell
docker-compose restart
```
>[!info]
>重啟一個或多個服務。

**執行命令**
```shell
docker-compose exec
```
在運行的容器中執行命令。例如，進入bash：
```shell
docker-compose exec service_name bash
```
>[!info]
>其中`service_name`是`docker-compose.yml`文件中定義的服務名稱。

**停止服務**
```shell
docker-compose stop
```
>[!info]
>停止一個或多個正在運行的服務，但不會移除它們。

**啟動已停止的服務**
```shell
docker-compose start
```
>[!info]
>啟動之前已經停止的服務。

**構建（或重新構建）`docker-compose.yml` 文件中定義的所有服務的映像，並啟動這些服務**
```shell
docker-compose up --build
```

**啟動`docker-compose.yml`檔案中定義的所有服務，並且會刪除那些不再由設定檔定義的「孤兒」容器。這是在設定檔中的服務清單發生變化時，清理不再需要的容器的有效方法。**
```shell
docker-compose up --remove-orphans
```

**移除一個或多個已停止的容器**
```shell
docker-compose rm
```
>[!info]
>- `-f` 或 `--force`: 強制移除容器，不會有確認提示。
>- `-s` 或 `--stop`: 停止容器，然後移除它們。
>- `-v`: 同時移除與容器關聯的匿名卷（不包括命名卷）。

**查看目前的 docker-compose.yml 設置**
```yml
docker-compose config
```