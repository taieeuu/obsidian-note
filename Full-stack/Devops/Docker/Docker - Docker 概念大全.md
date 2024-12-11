---
Date: 2024-06-01
tags:
  - docker
  - devops
---
## Docker 命令結構
Docker結構在於先輸入`docker`然後加上你要操作的對象以及那個對象要做甚麼事情，如下舉例。

操作列出image
```shell
docker image ls
```

列出當前運行的容器，(提外: 這邊的`ps`與`ls`一樣的意思，你可能發現`--help`中沒有這個指令，因為`ps`為早期所使用，只是沿用至今)
```shell
docker container ps
```

列出當前所有的容器
```shell
docker container ps -a
```

## Image vs Container 鏡像 vs 容器
### Image鏡像
- Docker image 是一個 read-only 文件
- 文件包含系統、源碼、庫文件、依賴、工具等一些運行application所需的文件
- docker image具有分層的概念
### Container 容器
- 一個運行中的docker image
- 實質是複製image並在image最上層加上一層`read-write`的層（為容器層）`container layer`
- 基於同一個image可以建立多個container

![[Docker 1.png|500]]

## Container 運行模式
主要分為兩種模式，1. Attach 模式. 2. detach 模式
- attach 模式
```shell
docker container run -p 80:80 nginx
```
>[!Info]
>- 透過這種方式創建容器的話，容器在前台執行
>- 容器的輸入輸出結果會反映在本地端，本地端的輸入輸出也會反映到容器，例如能在終端機看到網頁瀏覽器的 log，ctrl + c 會讓容器停止執行
>- 一般情況不建議使用
- detach 模式
```shell
docker container run -d -p 80:80 nginx
```
>[!Info]
>- 容器會在背景執行

## 連接容器 Shell
創建一個容器並進入交互模式
```shell
docker container run -it busybox sh
```
**注意** 
- `-it` 是讓你在創建時就進入容器內部，所以當你在`exit`退出時，容器也會stop。
- sh 使用container中的shell

在一個已經運行的容器裡執行一個額外的Command
```shell
docker container exec -it container_id sh
```

## Container vs VM
![[Pasted image 20240601101725.png|500]]

由上圖所知，所以容器並不是一個VM
- 容器其實是在地端運行的一個進程
- 當進程結束也就退出了

下面圖示，你可以看到`COMMAND`就是容器運行的腳本，是一個啟動腳本。負責配置和啟動MongoDB服務器當容器初始化時運行。
![[Pasted image 20240601102212.png]]

然後我們可以通過 `docker container top <container_name>`來查看容器內所有的進程。
![[Pasted image 20240601103656.png|1225]]
>[!Info]
>- 我們可以看到列印出來的 PID 是 9864、9930 ，也就是我們在系統所看到的進程，而底下的`pstree -halps <pid>`是 OS用來顯示進程的樹狀圖。
>- 在docker內部中，進程又與地端的進程 PID 不相同。

**補充**  
- PID為運行進程時，系統自動分配的，用於唯一標識此程序的一個整數。
- PPID為進程的ppid就是進程的父進程的pid
## Docker Container run背後發生甚麼事?
```shell
docker container run -d --publish 80:80 --name webhost nginx
```
>[!Info]
>1. 在本地查找是否有nginx這個image鏡像，但沒有發現
>2. 到遠端的image registry查找nginx映像（預設的registry是Docker Hub)
>3. 下載最新版本的nginx鏡像 （nginx:latest 預設)
>4. 基於nginx鏡像來建立一個新的容器，並且準備運行
>5. docker engine分配給這個容器一個虛擬IP位址
>6. 在宿主機上開啟80埠並把容器的80埠轉送到宿主機上
>7. 啟動容器，執行指定的命令（這裡是一個shell腳本去啟動nginx）

## Dockerfile
- Dockerfile是用來建構docker映像的文件
- Dockerfile裡包含了建置映像所需的“指令”
- Dockerfile有其特定的語法規則
