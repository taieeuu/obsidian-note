---
Date: 2024-06-04
tags:
  - docker
  - devops
---
>[!info]
>這邊只記載常用的指令，詳細可參閱官方文檔

## Docker Swarm 基本操作
激活swarm模式
```shell
docker info
```

初始化
```shell
docker swarm init
```

### 查看
查看swarm服務
```shell
docker service ls
```

查看 service replicate 中的 container，顯示的是服務層級的任務
```shell
docker service ps <id>
```

查看 service container，顯示的是具體的容器
```shell
docker container ps
```

列出 Docker Swarm 集群中的所有節點，**這邊只有swarm manager可以執行**
```shell
docker node ls
```

**查看詳細的內容 service**
```shell
docker service inspect <service-name>
```

**查看 service log**
```shell
docker service logs <service-name>
```
### 創建
創建服務
```shell
docker service create nginx:latest
```
### 刪除
刪除swarm服務
```shell
docker service rm <id>
```

刪除container，**但是swarm會自動添加容器補足**
```shell
docker container rm -f <id>
```

**副本集** 兩個都可使用
```shell
docker service update <id> --replicas <num>
```

```shell
docker service scale <service-name> =<num>
```