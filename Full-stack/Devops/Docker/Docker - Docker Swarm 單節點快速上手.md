---
Date: 2024-06-04
tags:
  - docker
  - devops
---
## Swarm單節點快速上手
### 初始化
`docker info`這個可以查看我們的docker引擎有沒有啟動swarm模式，預設是沒有的，我們看到會
```shell
Swarm: inactive
```

啟動swarm，有兩種方法：
- 初始化一個swarm集群，自己成為經理
- 加入一個已經存在的 swarm 集群
```shell
PS C:\Users\Peng Xiao\code-demo> docker swarm init
Swarm initialized: current node (vjtstrkxntsacyjtvl18hcbe4) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-33ci17l1n34fh6v4r1qq8qmocjo347saeuer2xrxflrn25jgjx-7vphgu8a0gsa4anof6ffrgwqb 192.168.65.3:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

PS C:\Users\Peng Xiao\code-demo> docker node ls
ID                            HOSTNAME         STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
vjtstrkxntsacyjtvl18hcbe4 *   docker-desktop   Ready     Active         Leader           20.10.7
PS C:\Users\Peng Xiao\code-demo>
```
### docker swarm init 背後發生了什麼事
主要是PKI和安全相關的自動化
- 建立 swarm 叢集的根憑證
- 管理者節點的證書
- 其它節點加入叢集需要的代幣

建立Raft資料庫用於證書儲存、配置、密碼等數據
RAFT相關資料
- [http://thesecretlivesofdata.com/raft/](http://thesecretlivesofdata.com/raft/)
- [https://raft.github.io/](https://raft.github.io/)
- [https://docs.docker.com/engine/swarm/raft/](https://docs.docker.com/engine/swarm/raft/)

看動畫學會 Raft 演算法
[https://mp.weixin.qq.com/s/p8qBcIhM04REuQ-uG4gnbw](https://mp.weixin.qq.com/s/p8qBcIhM04REuQ-uG4gnbw)

## 牛刀小試
我們可以先機活swarm mode
```shell
docker info
```

然後再初始化
```shell
docker swarm init
```

創建 service 
```shell
docker service create nginx:latest
```
![[Pasted image 20240604213446.png]]
>[!info]
>- `docker service ps`中的 id 顯示的是服務層級的任務
>- `docker container ls`中的 id 顯示的是具體的容器
>  所以這邊的 id 是不同的意思

我們也可以創建 replicas ，使用這個指令，如果當中有container 掛掉，系統會自動補足
```shell
docker service update <id> --replicas <num>
```
