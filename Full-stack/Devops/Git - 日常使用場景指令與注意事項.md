---
Date: 2024-09-17
tags:
  - git
  - devops
---
### 1. 不小心將變更推送到 GitHub，想要取消推送
當你不小心將錯誤的提交推送到遠端儲存庫（例如 GitHub），你可以採取以下步驟來撤銷這次推送：
#### 情境一：撤銷最近一次的推送（尚未被其他人拉取）
##### 步驟
1. 重設本地分支到上一次正確的提交

```sh
git reset --hard HEAD~1
```

- 這會將當前分支重設到上一次提交，並且捨棄最近的提交。

2. 強制推送到遠端儲存庫

```sh
git push origin <branch_name> --force
```

**注意：**
- **強制推送可能會影響其他協作開發者**，請確保與團隊溝通後再進行此操作。
- 如果不希望改變提交歷史，可以考慮使用 **`git revert`** 來撤銷特定的提交，而不是重設歷史。
#### 情境二：撤銷多個錯誤提交
如果你需要撤銷多個錯誤的提交，可以將 `HEAD~1` 替換為相應的提交數量。例如，要撤銷最近 3 次提交：

```sh
git reset --hard HEAD~3
git push origin <branch_name> --force
```
#### 情境三：撤銷推送後保留變更為未提交狀態
如果你希望撤銷推送，但保留變更在工作目錄中，可以使用 `--soft` 或 `--mixed` 選項：

```sh
git reset --soft HEAD~1
git push origin <branch_name> --force
```

- `--soft`：保留所有變更在暫存區。
- `--mixed`（預設）：保留變更在工作目錄中，但不在暫存區。
### 當另一個人的分支有更新，你的分支也有更新，如何處理合併到 master 分支
##### 步驟
1. 確保本地的 master 分支是最新的

```sh
git checkout master

git pull origin master
```

2. 切換到你的功能分支並合併 master 的變更

```sh
git checkout <feature_branch>
```

- **處理衝突（如有）：** 如果在合併過程中出現衝突，Git 會提示你手動解決衝突。打開相關檔案，根據需要保留或修改內容，並提交。

3. 將功能分支合併回 master

```sh
git checkout master

# 合併功能分支到 master
git merge feature-branch

git push origin master
```
### 撤銷已推送的敏感資訊（如密碼、API 金鑰）
如果你不小心將敏感資訊推送到 GitHub，應立即撤銷這些資訊並清理 Git 歷史。

