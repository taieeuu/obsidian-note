---
Date: 2024-06-01
tags:
  - docker
  - devops
---
>[!info]
>這邊只記載常用的指令，詳細可參閱[官方文檔](https://docs.docker.com/engine/)
## 鏡像基本操作
### 拉取
預設從Docker Hub拉取，如果不指定版本，會拉取最新版
```shell
docker pull nginx
```

指定版本
```
docker pull nginx:1.20.0
```
### 查看
查看鏡像有哪些
```shell
docker image ls
```

查看dockerfile內容
```shell
more <Dockerfile位置>
```

查看詳細容器內容
```shell
docker container inspect <container-id>
docker container volume <volume-id>
```

又或是你可以針對詳細內容去過濾其他你不想要的內容
```shell
docker container inspect --format '{{.NetworkSettings.IPAddress}}' client
```

報錯時，我們可以查看 container 的log
```shell
docker logs -f web5
```
>[!info]
>- `docker logs`：這是 Docker 的命令，用來顯示一個容器的日志。這些日志通常包括容器的標準輸出（STDOUT）和標準錯誤輸出（STDERR）。
>- `-f`：這個選項表示“follow”，意思是命令不僅會輸出目前的日志，還會持續輸出容器產生的新日志。這對於監控容器的實時活動非常有用。
>- `web5`：這是容器的名稱或 ID。在這個例子中，`web5` 是容器的名稱。你需要確保這個名稱指向一個實際存在且正在運行的容器。

從 terminal 進入以運行 container 中
```shell
docker container exec -it box1 ip a
```
>[!info]
>- `exec`：這是 Docker 的一個命令，用於在運行中的容器內部執行指令。
>- `-it`：
>	- `-i`（互動模式）：保持標準輸入（STDIN）開放，允許與執行的命令互動。
>	- `-t`：分配一個終端（pseudo-TTY），使得輸出更適合用戶閱讀。
>- `box1`：這是要執行命令的容器的名稱。你需要確保有一個名為 `box1` 的容器正在運行。
>- `ip a`：這是在容器內執行的命令，可以替換 linux 中的指令。`ip a` 是 `ip address` 的縮寫，用於列出容器內所有網絡接口的配置信息，包括 IP 地址、子網掩碼等。

查看 Docker 映像的歷史紀錄。這個命令會列出構建該 Docker 映像的每一層的詳細信息，以及每一層是如何創建的
```shell
docker image history <docker-image-name>
```
>[!info]
>輸出內容
>- **IMAGE**: 每一層的唯一 ID。
>- **CREATED**: 顯示該層是何時創建的。
>- **CREATED BY**: 列出創建該層的命令，這可能是一條 `RUN` 指令或其他 Docker 指令。
>- **SIZE**: 顯示該層的大小。
>- **COMMENT**: 如果有的話，顯示關於該層的註釋。

![[Pasted image 20240601200324.png]]
### 刪除
鏡像刪除， 容器同理。
```shell
docker image rm <image_id>
```

清除docker沒有使用到的容器，分兩種。image同理。
第一種
```shell
docker system prune -f
```
第二種
```shell
docker container rm $(docker container -aq)
```
## 創建
建構 image
```shell
docker image build -t <image-name> -f <dockerfile-name>.
```
>[!info]
>- `-t`: 這個選項用於為新建的映像指定一個標籤（tag），這樣你就可以在日後引用這個映像時使用這個名稱`<image-name>`。
>- `-f` : 指定使用 `<dockerfile-name>` 文件进行构建。
>- `.` : Docker 會根據當前目錄下的 `Dockerfile` 中定義的步驟來構建一個新的映像。
## 運行
運行容器
```shell
docker container run -it 123
```
>[!info]
>- `-it` : 進入背後運行

運行容器，且當容器執行完畢後，刪除
```shell
docker container run --rm -it 123
```
>[!info]
>- `--rm`: 在容器停止後自動刪除該容器

運行容器，並對容器命名
```shell
docker container run -d --name flask-root flask-demo
```
>[!info]
>- `-d`：這個選項代表“detached”模式，即容器將在後台運行。你不會看到容器的即時輸出，但可以透過 `docker logs` 命令來查看輸出。
>- `--name flask-root`：這個選項用於為運行的容器指定一個名稱，這裡指定的名稱是 `flask-root`。指定名稱可以方便後續管理容器，如停止、啟動或刪除容器。
>- `flask-demo`：這是 Docker 映像的名稱，容器將從這個映像中啟動。這個映像應該包含所有運行 Flask 應用所需的依賴和配置。

