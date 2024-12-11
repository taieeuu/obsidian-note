---
Date: 2024-06-04
tags:
  - docker
  - devops
---
## Docker 非root 模式
如果真的有需求使用非root模式的話，docker其實也有提供，不過我們需要設定以下的環境變數
```shell
$ export DOCKER_HOST=unix:///run/user/1000/docker.sock
```

可參考[https://docs.docker.com/engine/security/rootless/](https://docs.docker.com/engine/security/rootless/)

設置好之後我們就可以使用了，但要注意的是創建的使用者會變成使用者的名稱，像是這邊使用者為vagrant，所以就是為 vagrant。
![[Pasted image 20240608134829.png]]

