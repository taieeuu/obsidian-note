---
Date: 2024-06-04
tags:
  - docker
  - devops
---
## What is Podman?
`Podman`是一個基於`Linux`系統的容器`daemon-less`引擎。`OCI`podman 可以運行在 root 或非 root 使用者模式。

Podman 是 Red Hat 在 2018 年推出的，來源程式碼開放。

官方網站[https://podman.io/](https://podman.io/)
OCI [https://opencontainers.org/](https://opencontainers.org/)
##  和 docker 的差別
- 底層的差別是podman是Daemonless的，而Docker在執行任務的時候，必須依賴後台的docker daemon
- podman不需要使用root使用者或root權限，所以比較安全。
- podman 可以建立 pod，pod 的概念和 Kubernetes 裡定義的 pod 類似
- podman 運行把映像和容器存放放在不同的位置，但是 docker 必須儲存在 docker 工程師所在的本地
- podman是傳統的fork-exec模式，而docker是 client-server架構
## Docker 架構
![[Pasted image 20240608113507.png]]
## Podman架構
![[Pasted image 20240608113524.png]]