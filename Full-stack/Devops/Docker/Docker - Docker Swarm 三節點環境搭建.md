---
Date: 2024-06-04
---
## 三節點創建環境
由於沒錢，所以本次環境使用  https://labs.play-with-docker.com  
再藉由本地的 ssh 連線過去，ps. 要先生成自己 email 的 key。

接者開始，首先當然的要先初始化
```shell
docker swarm init
```
>[!info]
>然後你會發現有誤，原因是因為機器上有許多的介面卡，所以我們需要加上`--advertise-addr`設置 ip 位址。

就會生成 token ，我們在個別去 server 上添加進去。**注意這邊只能在docker swarm manager使用 `docker node ls` 否則無法查看**

接者啟動我們得 nginx 服務
```shell
docker service create --name web nginx
```
![[Pasted image 20240604230224.png]]
>[!info]
>第一部分為 service id 
>第二部分為 顯示 service id 正詳細的內容
>第三部分為 證實了他在node1 執行，node2、node3沒有。

然後我們在使用以下命令進行 副本集
```shell
docker service update web --replicas 3
```
![[Pasted image 20240604230603.png]]
>[!info]
>證實了命令無誤，成功運行 !

接下來我們可以模擬當異常發生時，service 會如何處理
```
docker rm -f <id>
```
![[Pasted image 20240604230829.png]]
>[!info]
>這裡我們強制停止一個容器，然後再次查看服務，會發現他重新運行了一個容器。ps. 他不一定會在node1 重啟服務，看他的調度機制。
