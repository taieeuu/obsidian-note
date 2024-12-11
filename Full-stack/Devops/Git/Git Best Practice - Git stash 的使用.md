---
Date: 2024-07-20
tags:
  - devops
  - git
---
`git stash` 允許你將當前工作目錄中的修改保存起來，以便於切換到其他分支或進行其他操作。使用 `git stash` 可以臨時存放未提交的變更，然後隨時取回，保持工作流程的靈活性和高效性。
### 基本使用方法
##### 1. 保存工作狀態 (`git stash`)
當你在工作目錄中有未提交的變更，需要切換分支或執行其他操作時，可以使用 `git stash` 保存當前的工作狀態。

```shell
git stash
```

這將保存當前的工作目錄和暫存區的變更，並恢復到乾淨的工作目錄。
##### 2. 恢復工作狀態 (`git stash apply`)
這將應用最近一次的 stash，但不會移除 stash 列表中的該項目。

```shell
git stash apply
```

這將應用最近一次的 stash，但不會移除 stash 列表中的該項目。
##### 3. 恢復並移除工作狀態 (`git stash pop`)
如果你需要取回變更並從 stash 列表中移除該項目，可以使用 `git stash pop`。

```shell
git stash pop
```

這將應用最近一次的 stash 並將其從 stash 列表中移除。
### 進階使用方法
##### 1. 查看 stash 列表 (`git stash list`)
你可以使用 `git stash list` 查看所有保存的 stash。

```shell
git stash list
```

這將顯示類似以下的輸出：

```shell
stash@{0}: WIP on branch: Commit message
stash@{1}: WIP on branch: Commit message
```
##### 2. 指定應用特定的 stash (`git stash apply` 或 `git stash pop`)
如果你有多個 stash，可以指定應用特定的 stash。

```shell
git stash apply stash@{1}
or
git stash pop stash@{1}
```
##### 3. 保存帶訊息的 stash (`git stash save`)
你可以在保存 stash 時添加描述訊息，方便以後辨識。

```shell
git stash save "My work in progress"
```
##### 4. 保存部分文件的變更 (`git stash -p`)
如果你只想保存部分文件的變更，可以使用 `git stash -p` 進行交互式選擇。

```shell
git stash -p
```
##### 5. 清理 stash 列表 (`git stash clear`)
當你不再需要 stash 列表中的變更時，可以使用 `git stash clear` 清空 stash 列表。

```shell
git stash clear
```
### 示例
假設你在分支 `feature` 上工作，並有一些未提交的變更。突然，你需要切換到 `main` 分支進行緊急修補。以下是使用 `git stash` 的典型流程：
##### 1. 保存當前工作狀態：
```shell
git stash
```
##### 2. 切換到 `main` 分支並進行緊急修補
```shell
git checkout main # 執行修補操作 git add . git commit -m "Fix urgent bug"
```
##### 3. 切換回 `feature` 分支並恢復工作狀態
```shell
git checkout feature git stash pop
```
### 注意事項
1. **檢查工作目錄是否乾淨**： 在使用 `git stash` 之前，確保你的工作目錄是乾淨的，沒有未保存的重要變更。
2. **避免丟失重要變更**： 在進行大範圍的變更之前，最好將重要的變更提交到本地分支，以避免意外丟失。
3. **理解 stash 的行為**： `git stash` 會保存未跟蹤的文件（使用 `-u` 或 `--include-untracked` 選項），但不會保存忽略的文件（除非使用 `-a` 或 `--all` 選項）。