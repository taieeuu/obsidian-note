---
Date: 2024-06-02
tags:
  - docker
  - devops
---
>[!info]
>此篇只講述概念以及基本語法、應用，詳細可參閱[官方文檔1](https://docs.docker.com/compose/compose-file/)[官方文檔2](https://docker-docs.uclv.cu/compose/compose-file/#healthcheck)
## 基本語法結構
```yml
version: "3.8"

services: # 容器
	 servicename: # 服務名字，這個名字也是內部 bridge網絡可以使用的 DNS name
		 image: # 鏡像的名字
		 command: # 可選，如果設置，則會覆蓋默認鏡像裡的 CMD命令
		 environment: # 可選，相當於 docker run裡的 --env
		 volumes: # 可選，相當於docker run裡的 -v
		 networks: # 可選，相當於 docker run裡的 --network
		 ports: # 可選，相當於 docker run裡的 -p
	 servicename2:

volumes: # 可選，相當於 docker volume create

networks: # 可選，相當於 docker network create
```

以在 docker network 網路中練習的 Python Flask + Redis練習：為例子，改造成一個docker-compose文件
```shell
docker image pull redis
docker image build -t flask-demo .

# create network
docker network create -d bridge demo-network

# create container
docker container run -d --name redis-server --network demo-network redis
docker container run -d --network demo-network --name flask-demo --env REDIS_HOST=redis-server -p 5000:5000 flask-demo
```

docker-compose.yml

```yml
version: "3.8"

services:
  flask-demo:
    image: flask-demo:latest
    environment:
      - REDIS_HOST=redis-server
    networks:
      - demo-network
    ports:
      - 8080:5000

  redis-server:
    image: redis:latest
    networks:
     - demo-network

networks:
  demo-network:
```
---
## docker-compose 鏡像構建和拉取
如果說我們在本地沒有查詢到擁有該鏡像的話，會去 docker hub 中拉取。
那當然我們也可以指定我們的 dockerfile 的路徑當作鏡像

**指定Dockerfile路徑**
```yml
version: "3.8"

services:
  flask-demo:
	build: ./flask
    image: flask-demo:latest
    environment:
      - REDIS_HOST=redis-server
    networks:
      - demo-network
    ports:
      - 8080:5000

  redis-server:
    image: redis:latest
    networks:
     - demo-network

networks:
  demo-network:
```
>[!info]
>**注意** 這邊的`build`: 指定你要的 dockerfile 進行構建，而在下面的`image`則會變成重新命名你構建的鏡像名稱。

**指定Dockerfile路徑以及名稱**
```yml
version: "3.8"

services:
  flask-demo:
	build:
		context: ./flask
		dockerfile: Dockerfile.dev
    image: flask-demo:latest
    environment:
      - REDIS_HOST=redis-server
    networks:
      - demo-network
    ports:
      - 8080:5000

  redis-server:
    image: redis:latest
    networks:
     - demo-network

networks:
  demo-network:
```
>[!info]
>- **build** : 指定如何建構這個服務的鏡像。
>	- **context** : 建構上下文目錄，即Dockerfile 所在的位置。
>	- **dockerfile** : 指定使用的Dockerfile 檔名。
>- **environment** : 系統環境變數列表，這裡設定了`REDIS_HOST`環境變量，使Flask 應用知道如何連接到Redis 服務。

---
## docker-compose 網路概念
我們使用 `dig` 來看容器內網路的設置
```shell
dig box2
```
>[!info]
>它主要用於查詢 DNS (Domain Name System) 相關資訊。使用 `dig` 命令可以獲得非常詳細的 DNS 查詢回應，包括問題部分、回答部分、授權部分和附加部分等。

![[Pasted image 20240602192853.png]]
>[!info]
>在容器內，我們可以看到 box2 的 IP位址為 172.18.0.3 他訪問的 server 為 127.0.0.11:53，這是我們內部 DNS 的 Server，是由 docker engine所維護的，這在地端主機上實際上也是可以 ping 通的，**但是在主機上我們是 ping 不通 box1 的，因為主機上所使用的 DNS 並不是 127.0.0.11 **

我們在主機上查看 resolv.conf，也證實他使用的 DNS 是 10.0.2.3。
![[Pasted image 20240602193409.png]]

那我們可以在 docker-compose 中，ping container name原因是因為它會自動的幫我們創建一個 network ，且將我們的 container name 寫入到 DNS Server中。

---
## Networks 配置

看到以下的docker-compose，有一個 container 有一個 network ，另一個則有兩個 network ，我們也可以進入容器中查看他們的 ip 位址 有確實證實了有配置成功。
```yml
version: "3.8"

services:
  box1:
    image: net-box:latest
    command: /bin/sh -c "while true; do sleep 3600; done"
	networks:
		- mynetwork1

  box2:
    image: net-box:latest
    command: /bin/sh -c "while true; do sleep 3600; done"
    networks:
     - mynetwork1
     - mynetwork2

networks:
     mynetwork1:
     mynetwork2:
```
![[Pasted image 20240602214133.png]]

那在那之下還有一個 networks ，這個地方我們可以針對上述的 network 進行 更詳細的配置。如下程式碼。

```yml
version: "3.8"

services:
  box1:
    image: net-box:latest
    command: /bin/sh -c "while true; do sleep 3600; done"
	networks:
		- mynetwork1

  box2:
    image: net-box:latest
    command: /bin/sh -c "while true; do sleep 3600; done"
    networks:
     - mynetwork1
     - mynetwork2

networks:
     mynetwork1:
	     ipam:
			driver: default
			config:
				- subnet: "172.16.238.0/24"
     mynetwork2:
	     ipam:
	      driver: default
```
>[!info]
> networks 配置允許您定義網路的子網和其他網路設置
> - IPAM
> 	- `driver`: 指定 IPAM 驅動，通常是 `default`。
> 	- `config`: 這裡可以設定一個或多個子網（subnet），它們定義了網路的 IP 範圍。

---
## 水平擴展 scale
docker-compose 也提供給我們可以同時啟動服務並根據指定的數量進行擴展（scaling）
```shell
docker-compose up -d --scale flask=3
```
>[!info]
>- `-d`：這個選項意味著以分離模式（detached mode）運行容器，即在背景中運行。
>- `--scale SERVICE=NUM`：這裡 `SERVICE` 是你在 `docker-compose.yml` 中定義的服務名稱，`NUM` 是你希望運行的該服務容器的實例數量。

![[Pasted image 20240602222343.png]]

實作的部分我們可以利用這個[源碼](https://dockertips.readthedocs.io/en/latest/_downloads/15ef8ef4c424aefda9ce24c71698051d/compose-scale-example-2.zip)來進行

不過還是讓我們來說明此處docker-compose源碼。
```yml
version: "3.8"

services:
  flask:
    build:
      context: ./flask
      dockerfile: Dockerfile
    image: flask-demo:latest
    environment:
      - REDIS_HOST=redis-server
    networks:
      - backend
      - frontend
  redis-server:
    image: redis:latest
    networks:
      - backend

  nginx:
    image: nginx:stable-alpine
    ports:
      - 8000:80
    depends_on:
      - flask
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf:ro
      - ./var/log/nginx:/var/log/nginx
    networks:
      - frontend

networks:
  backend:
  frontend:
```

**Flask**
```yml
services:
  flask:
    build:
      context: ./flask
      dockerfile: Dockerfile
    image: flask-demo:latest
    environment:
      - REDIS_HOST=redis-server
    networks:
      - backend
      - frontend
```
>[!info]
>- **build:** 指定了建構 Flask 映像檔的上下文和 Dockerfile 的位置。
>- **image:** 指定了建構後的映像檔名稱為 `flask-demo:latest`。
>- **environment:** 設定環境變數 `REDIS_HOST`，其值為 `redis-server`，這將指向 Redis 伺服器。
>- **networks:** 指定 Flask 服務連接到 `backend` 和 `frontend` 這兩個網路。

**Redis**
```yml
redis-server:
    image: redis:latest
    networks:
      - backend
```
>[!info]
>- **image:** 使用官方的 Redis 映像檔 `redis:latest`。
>- **networks:** 指定 Redis 服務連接到 `backend` 網路。

**Nginx 服務**
```yml
  nginx:
    image: nginx:stable-alpine
    ports:
      - 8000:80
    depends_on:
      - flask
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf:ro
      - ./var/log/nginx:/var/log/nginx
    networks:
      - frontend
```
>[!info]
>- **image:** 使用 Nginx 映像檔 `nginx:stable-alpine`。
>- **ports:** 將宿主機的 8000 端口映射到容器內的 80 端口。
>- **depends_on:** 指定 Nginx 依賴 Flask 服務，確保 Flask 服務先啟動。
>- **volumes:**   - 將本地的 `./nginx/nginx.conf` 文件掛載到容器內的 `/etc/nginx/conf.d/default.conf`，並設定為只讀。   - 將本地的 `./var/log/nginx` 目錄掛載到容器內的 `/var/log/nginx`。
>- **networks:** 指定 Nginx 服務連接到 `frontend` 網路。
### 網路設定
```yml
networks:
  backend:
  frontend:
```
>[!info]
>定義了兩個網路 `backend` 和 `frontend`，各服務將根據需要連接到這些網路。這有助於隔離網路流量，並確保服務之間的互通性。
### 總結
此 Docker Compose 設定檔為一個簡單的微服務架構示例，使用 Flask 來處理應用邏輯，Redis 作為快取伺服器，Nginx 作為反向代理伺服器。透過設定不同的網路，確保服務之間的隔離和互通。

![[Pasted image 20240602223827.png]]
>[!info]
>上述實驗結果，我們在一開始我們啟動 nginx 代理了 80 端口，只有一個 flask 實例，後來我們運行 `docker-compose up -d --scale flask=3` 水平擴展，可以發現 ip 位址會不段的變化且 次數也會不段的 +1 ，成功 !!
## 修改上述的環境變量
```yml
version: "3.8"

services:
  flask:
    build:
      context: ./flask
      dockerfile: Dockerfile
    image: flask-demo:latest
    environment:
      - REDIS_HOST=redis-server
      - REDIS_PASS=${REDIS_PASSWORD}
    networks:
      - backend
      - frontend

  redis-server:
    image: redis:latest
    command: redis-server --requirepass ${REDIS_PASSWORD}
    networks:
      - backend

  nginx:
    image: nginx:stable-alpine
    ports:
      - 8000:80
    depends_on:
      - flask
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf:ro
      - ./var/log/nginx:/var/log/nginx
    networks:
      - frontend

networks:
  backend:
  frontend:
```
>[!info]
>可以看到我們的 flask 中的 `environment` 密碼有個 `${REDIS_PASSWORD}`進行安全性的設置，主要是讀取當前 docker-compose 同級的目錄的 .env 檔案 進行抓取的。
>如果要指定 env 檔案的話，可以使用 `docker-compose --env-file .\myenv`，這樣進行設置。
## Container Health
我們可以根據[官方文檔](https://docker-docs.uclv.cu/compose/compose-file/#healthcheck)所說操下來把它改成自己想要的。
```yml
version: "3.8"

services:
  flask:
    build:
      context: ./flask
      dockerfile: Dockerfile
    image: flask-demo:latest
    environment:
      - REDIS_HOST=redis-server
      - REDIS_PASS=${REDIS_PASSWORD}
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5000"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 40s
    depends_on:
      redis-server:
        condition: service_healthy
    networks:
      - backend
      - frontend

  redis-server:
    image: redis:latest
    command: redis-server --requirepass ${REDIS_PASSWORD}
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 1s
      timeout: 3s
      retries: 30
    networks:
      - backend
   
  nginx:
    image: nginx:stable-alpine
    ports:
      - 8000:80
    depends_on:
      flask:
        condition: service_healthy
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf:ro
      - ./var/log/nginx:/var/log/nginx
    networks:
      - frontend

networks:
  backend:
  frontend:
```
## docker compose 投票app 練習
原始碼位址：[https://github.com/dockersamples/example-voting-app](https://github.com/dockersamples/example-voting-app)
另外這個repo大家也可以收藏一下[https://github.com/docker/awesome-compose](https://github.com/docker/awesome-compose)裡面非常多的docker-compose example





