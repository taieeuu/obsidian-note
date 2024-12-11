---
Date: 2024-06-04
---
## Swarm 使用 local volume
在佈署之前，我們先清除 volume 資料，比較好觀察。(ps. 每個 service 都要)
```shell
docker volume ls

docker volume rm <volume-name>
```

docker-compose.yml
```yml
version: "3.8"

services:
  db:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD_FILE=/run/secrets/mysql_pass
    secrets:
      - mysql_pass
    volumes:
      - data:/var/lib/mysql

volumes:
  data:

secrets:
  mysql_pass:
    file: mysql_pass.txt
```

mysql_pass.txt
```txt
abc123
```

使用以下命令佈署
```shell
docker stack deployee --compose-file docker-compose.yml db
```
>[!info]
>這段會命令用於在 Docker Swarm 中部署一個 stack。顯示內容會有 network、secret
>service。
>參數說明:
>- `docker stack deploy`：這是 Docker Swarm 的一個命令，用於部署或更新一個 stack。Stack 是一組相關服務的集合，可以被看作是應用的整體。
>- `--compose-file docker-compose.yml`：指定使用的 Compose 文件。Compose 文件定義了 stack 中的服務、網絡和卷。`docker-compose.yml` 是這個 Compose 文件的名稱。  
>- `db`：這是 stack 的名稱。在這個例子中，stack 名稱是 `db`。部署後，可以使用這個名稱來管理和查看這個 stack。

查看 Docker Swarm 中的所有 stacks
```shell
docker stack ls
```
>[!info]
>Docker Swarm 集群中所有 stack 的列表，包括每個 stack 的名稱、服務數量和運行狀態等信息。

查看 Docker Swarm 所有服務
```shell
docker service ls
```
>[!info]
>顯示當前 Docker Swarm 集群中所有服務的列表，包括每個服務的 ID、名稱、模式、副本數量、映射的端口等信息。這有助於管理和監控 Swarm 中的服務狀態。

且使用下面命令查看他是位於哪個 Node 
```shell
docker service ps <serivce-name>
```
>[!info]
>回傳會有一個 Node 這就是他是位於哪個節點。然後在該節點可以使用 `docker volume ls`查看是否有佈署永久持化數據成功

**這邊的數據只能在該佈署節點執行，是本地與容器之間的映射關係，如果要使用其他方式像本地存儲、分佈式存儲、雲存儲等等，那就要更改`driver`這個部分了**