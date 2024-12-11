---
Date: 2024-07-14
tags:
  - devops
  - git
---
#### 刪除本地分支
在確認某個本地分支不再需要後，可以使用以下命令刪除它：
```shell
`git branch -d <分支名稱>
```

如果分支沒有被合併到主分支，並且仍然確定要刪除，可以使用 `-D` 選項強制刪除：
```shell
git branch -D <分支名稱>
```
#### 刪除遠程分支
要刪除遠程分支，可以使用以下命令：
```shell
git push origin --delete <分支名稱>
```
#### 刪除已經刪除的遠程分支在本地的引用
遠程分支被刪除後，本地的引用仍然會保留，這會使 `git branch -r` 命令的輸出變得雜亂。要清理這些已經刪除的遠程分支，可以使用以下命令：
```shell
git fetch -p
```
#### 查看未合併的本地分支

要查看哪些本地分支還未合併，可以使用以下命令：
```shell
git branch --no-merged
```
#### 查看未合併的遠程分支
要查看哪些遠程分支還未合併，可以使用以下命令：

```shell
git branch -r --no-merged
```
#### 查看已經合併的本地master分支
```shell
git branch -merged master
```
#### 自動化清理
可以編寫腳本來自動化分支清理過程。例如，使用以下腳本來自動刪除已合併到主分支的本地分支：

刪除所有已合併到 `master` 分支的本地分支。
```shell
git branch --merged master | grep -v "\*" | grep -v "master" | xargs -n 1 git branch -d`
```
