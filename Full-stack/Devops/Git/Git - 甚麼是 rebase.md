---
Date: 2024-07-06
tags:
  - git
  - devops
---
Git rebase 是一個將一個分支上的提交移動到另一個基礎上的操作。它通常用來整理提交歷史，使其更直觀和線性。

![[git_rebase.gif]]
## 基本語法
```shell
git rebase <branch>
```
## 操作步驟
1、確保工作區乾淨：

```shell
git status
```

2、開始 rebase：

```shell
git rebase main
```
    
    
3、遇到衝突時，解決衝突並繼續：

```shell
git add <解決衝突的檔案> 
git rebase --continue
```

4、若想放棄 rebase：

```shell
git rebase --abort
```
## 注意事項
- **避免在公共分支上使用 rebase：** 這會改變提交歷史，可能導致其他人的提交歷史混亂。
- **保持工作區乾淨：** 在 rebase 前確保沒有未提交的更改。