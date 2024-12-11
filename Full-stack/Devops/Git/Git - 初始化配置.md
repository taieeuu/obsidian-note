---
Date: 2024-07-06
tags:
  - devops
  - git
---
## 全域設定
```shell
git config --global user.name "Tai"
git config --global user.email "tai@gmail.com"
```
>[!info]
>在目前使用者目錄下有一個`.gitconfig`的檔案生成

查看設定
```shell
git config -l
```
---
## Git Init 
1、初始化 Git 儲存庫：
```shell
git init
```

初始化後，出現這些檔案，然後我們使用 `rm -rf *.sample*`把 sample 檔案刪除。
![[Pasted image 20240629220615.png]]
![[Pasted image 20240629221857.png]]
## 解說 `./.git/config`
這是本地專案的 Git 配置文件，存儲了專案的相關配置資訊。當在這個專案目錄下使用 Git 指令時，大多數情況下會依據這個配置文件中的資訊來執行操作。

```shell
git config -l
```
>[!info]
>ps. 這個指令可以顯示所有層級的 Git 配置，包括系統級、全域級（用戶級）和本地專案級的配置。根據執行指令時所在的路徑不同，顯示的配置內容也可能不同。

使用上述指令，我們會獲取到本地專案的 Git 配置，而如果一開始沒有配置資訊的話，會直接從全域的 git 設置去獲取。

我們可以通過以下方式來配置本地專案設定：
```shell
git config user.name = "Demo"
git config user.email = "demo@gmail.com"
```

配置完成後，使用 `cat .git/config` 可以查看已更新的本地專案配置文件：
```shell
cat .git/config
```

## 修改預設分支
可以使用以下指令來設置預設分支：
```shell
git config --global init.defaultBranch main
or
git config --global init.defaultBranch master
```
