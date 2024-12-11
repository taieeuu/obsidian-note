---
Date: 2024-07-10
tags:
  - devops
  - git
---
Git 中的 `origin` 是一個遠端代稱，通常指向你最初克隆的遠端倉庫。這是 Git 中遠端倉庫的默認名稱，用來追蹤與之交互的遠端倉庫地址。
### 檢視遠端倉庫
使用以下命令來查看當前倉庫的遠端倉庫：
```shell
git remote -v
```
這將列出所有遠端倉庫及其對應的 URL。

### 添加遠端倉庫
如果需要添加一個新的遠端倉庫，可以使用以下命令：
```shell
git remote add <name> <url>
```
e.g.  git remote add origin https://github.com/user/repo.git
### 刪除遠端倉庫
如果需要刪除遠端倉庫，可以使用以下命令：
```shell
git remote remove <name>
```
### 重命名遠端倉庫
如果需要重命名遠端倉庫，可以使用以下命令：
```shell
git remote rename <old-name> <new-name>
```
## 查看.git 變化
### .git/config
這個文件包含了倉庫的配置，包括遠端倉庫的設置。當你添加 `origin` 遠端倉庫後，`config` 文件會有類似以下的條目：
```shell
[remote "origin"]
        url = https://github.com/taieeuu/test.git
        fetch = +refs/heads/*:refs/remotes/origin/*
```
### `.git` 目錄結構
當你添加或修改遠端倉庫後，`.git` 目錄會發生一些變化。以下是 `.git` 目錄的一個示例結構：
```shell
PS D:\Project\Note\git\git-demo> tree .git/
列出磁碟區 Data 的資料夾 PATH
磁碟區序號為 1C4C-DFEA
D:\PROJECT\NOTE\GIT\GIT-DEMO\.GIT
├─hooks
├─info
├─logs
│  └─refs
│      ├─heads
│      └─remotes
│          └─origin
├─objects
│  ├─0d
│  ├─18
│  ├─19
│  ├─1f
│  ├─25
│  ├─2a
│  ├─35
│  ├─96
│  ├─cd
│  ├─e7
│  ├─info
│  └─pack
└─refs
    ├─heads
    ├─remotes
    │  └─origin
    └─tags
```
>[!info]
>當你添加遠端倉庫 `origin` 後，`.git/refs/remotes/origin/` 以及 `.git/log/refs/remotes/origin/` 目錄會被創建，用於存放來自 `origin` 的遠端分支引用。
### 遠端倉庫的影響
當你添加遠端倉庫 `origin` 後，會有以下變化：
- `.git/refs/remotes/origin/`：存放來自遠端倉庫 `origin` 的分支引用。
- `.git/logs/refs/remotes/origin/`：記錄與遠端倉庫 `origin` 相關的操作日誌。
### 注意事項
- 可以利用 `git ls-remote origin` 查看遠端倉庫的所有引用。
- 確保 `git remote -v` 可以正確列出遠端倉庫的地址和名稱。