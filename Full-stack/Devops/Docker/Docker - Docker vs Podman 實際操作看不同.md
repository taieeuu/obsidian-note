---
Date: 2024-06-04
tags:
  - docker
  - devops
---
## Docker vs Podman 實際操作的不同
我們使用以下命令，查找與 Docker 相關的正在運行的進程。
```shell
ps -ef | grep docker
```
>[!info]
>參數說明:
>1. `ps` 是一個顯示當前系統上正在運行的進程的命令。它的選項 `-ef` 表示：
>- `-e`：列出所有進程，不管是哪個用戶啟動的。
>- `-f`：以完整格式顯示進程信息，包括 PID、PPID、啟動時間、執行的命令等。
>2. `|`
>- 管道符 `|` 將前一個命令的輸出作為下一個命令的輸入。在這裡，它將 `ps -ef` 的輸出傳遞給 `grep` 命令。
>3. `grep docker`
>- `grep` 是一個搜索命令，用於在輸入數據中查找與指定模式匹配的行。在這裡，`grep docker` 用於查找包含 `docker` 字符串的行。

然後我們會發現他有個 root 用戶所啟動的 dockerd 的進程

而對於我們的 podman 來說，他沒有所謂的 podman、docker其他的進程的
```shell
ps -ef | grep podman
```

docker 是一個 client-server 的架構，dockerd 這邊是一個 server ，而 docker 是一個 client，像是使用 `docker image ls` 他就會去連接 dockerd 這樣一個 server。

### 根權限
- **Docker**：Docker Daemon 通常以 root 身份運行，這意味著所有 Docker 命令都需要 root 權限。
- **Podman**：Podman 支持無根模式，可以讓普通用戶運行容器，增強了安全性。
### 命令兼容性
大多數 Docker 命令都可以在 Podman 中使用，因為 Podman 設計時考慮到了命令的兼容性。然而，也存在一些細微的差異。
### 配置和文件路徑
- **Docker**：Docker 使用 `/var/lib/docker` 作為默認存儲位置。
- **Podman**：Podman 的默認存儲位置是 `/var/lib/containers`。
### 編排
- **Docker**：Docker 自帶 Docker Compose 和 Docker Swarm 作為編排工具。
- **Podman**：Podman 不包含內建的編排工具，但可以與 Kubernetes 和其他工具集成使用。
### 配置文件
- **Docker**：使用 `/etc/docker/daemon.json` 來配置 Docker Daemon。
- **Podman**：Podman 沒有守護進程，所以配置主要通過命令行參數或環境變量來完成。
### 安全性
Podman 無需 root 權限運行容器，這使得它在安全性方面具有優勢，特別是在需要多用戶管理容器的環境中，在作業系統中每個使用者的環境之間都會將其隔離開來，使用了最小隔離原則，