---
Date: 2024-07-19
tags:
  - git
  - devops
---
`git cherry-pick` 是 Git 中的一個強大指令，它允許你從一個分支選擇一個或多個提交 (commit) 並應用到另一個分支。這個指令在你希望**從其他分支提取特定功能或修正而不需要合併整個分支**時非常有用，像是一開始 `master` 與 `pk` 分支同樣進度，但途中 `master` 分支有新增功能，也有 `Fix Bug` ，所以當我們 `pk` 分支不需要其他功能，而只需 `Fix Bug` 時就會使用到 `git cherry-pick`

### 基本使用方式
**切換到目標分支**： 你需要首先切換到你希望應用提交的目標分支，例如：
```shell
git checkout master
```
    
**查找要提取的提交**： 使用 `git log` 或其他方式找到你想要提取的提交的哈希值
```shell
git log
```
    
**執行 cherry-pick**： 使用 `git cherry-pick <commit-hash>` 來提取提交。
```shell
git cherry-pick abc123
```

這將會將 `abc123` 這個提交應用到當前分支。
### 高級使用方式
**多個提交**： 如果你想一次提取多個提交，你可以使用範圍：
```shell
git cherry-pick abc123..def456
```
    
這將會提取從 `abc123` 到 `def456`（不包括 `abc123`）之間的所有提交。
    
**解決衝突**： 在執行 `git cherry-pick` 時，如果遇到衝突，Git 會停下來讓你手動解決衝突。解決完衝突後，使用以下命令來完成 cherry-pick：
```shell
git add . git cherry-pick --continue
```
    
    
如果你想中止 cherry-pick，可以使用：
```shell
git cherry-pick --abort
```
    
    
**編輯提交信息**： 如果你希望在 cherry-pick 之後編輯提交信息，可以使用 `-e` 標誌：
```shell
git cherry-pick -e abc123
```
    
**自動解決合併**： 使用 `-x` 標誌可以在提交信息中添加被選取提交的哈希值，以便於追蹤：
```shell
git cherry-pick -x abc123
```
    
### 實際案例

假設你有兩個分支 `feature-branch` 和 `master`，你在 `feature-branch` 上完成了一個修正並提交了它。現在你希望將這個修正應用到 `master` 分支上，而不合併整個 `feature-branch`。你可以這樣做：

1. 切換到 `master` 分支：
```shell
git checkout master
```
    
    
2. 查找在 `feature-branch` 上的修正提交哈希值：
```shell
git log feature-branch
```
    
3. 使用 `cherry-pick` 提取修正：
```shell
git cherry-pick abc123
```

這樣，就成功地將 `feature-branch` 上的特定修正應用到了 `master` 分支。

`git cherry-pick` 為你的版本控制工作流提供了很大的靈活性，可以精細地控制哪些修改應該出現在不同的分支上。