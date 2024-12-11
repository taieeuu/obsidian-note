---
Date: 2024-07-14
tags:
  - devops
  - git
---
### Git 遠端分支
#### 分支建立：
- GitHub 網頁
- `git push -u origin <branch_name>` 將本地分支 `<branch_name>` 推送到遠端分支 `dev`，如果 `<branch_name>` 分支在遠端不存在，則會被創建。
#### 分支刪除：
- GitHub 網頁
- `git branch -d <branch_name>` 刪除本地分支
- `git push origin -d <branch_name>` 刪除遠端分支
#### 列出所有本地和遠端分支：
- `git branch -a` 列出所有本地和遠端分支
- `git branch -r` 列出所有遠端分支
- `git branch -vv` 列出所有本地分支的跟蹤信息
#### 同步遠端分支：
- `git fetch` 下載遠端倉庫的對象和引用
    - `--prune` 在獲取之前刪除不再存在於遠端的任何跟蹤引用
- `git pull` 獲取並合併遠端到本地跟蹤分支
### Git 遠端
- `git remote add/remove <name> <url>` 添加/移除一個遠端
- `git remote` 獲取遠端名稱，默認為 `origin`
    - `-v` (verbose) 顯示更多信息，如遠端 URL
- `git remote show origin` 顯示遠端 `origin` 的跟蹤信息，需要連接到互聯網
- `git remote prune origin` 刪除本地不存在於遠端的 `origin` 分支


`git show-ref` 指令用於顯示所有引用（refs），包括分支、標籤等。這些引用指向的是 Git 資料庫中的特定提交（commit）