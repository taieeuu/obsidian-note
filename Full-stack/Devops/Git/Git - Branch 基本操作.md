---
Date: 2024-07-04
tags:
  - devops
  - git
---
## 基本操作
- **`git branch`**：
    - **用途**：列出當前倉庫中所有的分支。
    - **筆記**：這個指令不會改變任何內容，只是顯示分支列表。
- **`git branch <branch_name>`**：
    - **用途**：建立一個新的分支，名稱為 `<branch_name>`。
    - **筆記**：如果已有相同名稱的分支，這個操作會返回錯誤訊息。
- **`git branch -D <branch_name>`**：
    - **用途**：強制刪除分支 `<branch_name>`。
    - **筆記**：
        - 不能刪除當前所在的分支。
        - 如果指定的分支不存在，會返回錯誤訊息。
- **`git checkout`**：
    - **用途**：切換到指定的分支。
    - **筆記**：這個指令改變當前活動的分支。使用此指令可以在不同的分支間切換工作。
- **git checkout -b <branch_name>**:
	- **用途**：建立一個新的分支並立即切換到該分支。
	- **筆記**：這個指令相當於同時執行 `git branch <branch_name>` 和 `git checkout <branch_name>`，方便快捷地開始在新分支上工作。
- **git branch -m <old_name> <new_name>**
	- **用途**：重命名分支。
	- **筆記**：這個指令可以用來改變當前分支或指定分支的名稱。如果 `<new_name>` 已存在，會返回錯誤訊息。
## Demo
```python
PS D:\Project\Note\Git\git-demo> git branch dev
PS D:\Project\Note\Git\git-demo> git branch
  dev
* master
PS D:\Project\Note\Git\git-demo> git log
commit abe59448e29b1b11d26a4876c0ec5e6fe8c3ea01 (HEAD -> master, dev)
Author: taieeuu <taieeuu@gmail.com>
Date:   Sun Jun 30 15:24:38 2024 +0800

    3rd commit

commit c59bdf3151a48b12ba79dd339cd50eca9cd5c120
Author: taieeuu <taieeuu@gmail.com>
Date:   Sun Jun 30 15:05:10 2024 +0800

    2nd commit

commit 041734cceb2ff014250a2cad2c762a631780fd30
Author: taieeuu <taieeuu@gmail.com>
Date:   Sun Jun 30 13:25:43 2024 +0800

    1st commit
PS D:\Project\Note\Git\git-demo> cat .git/refs/heads/dev
abe59448e29b1b11d26a4876c0ec5e6fe8c3ea01
PS D:\Project\Note\Git\git-demo> cat .git/refs/heads/master
abe59448e29b1b11d26a4876c0ec5e6fe8c3ea01
PS D:\Project\Note\Git\git-demo> cat .git/HEAD
ref: refs/heads/master
PS D:\Project\Note\Git\git-demo> git checkout dev
Switched to branch 'dev'
PS D:\Project\Note\Git\git-demo> git branch
* dev
  master
PS D:\Project\Note\Git\git-demo> cat .git/HEAD
ref: refs/heads/dev
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>在一個目錄下建立了 `dev` 分支，並切換到這個新分支。驗證可以發現`refs/heads/master` and `refs/heads/dev` 都指向同一個 commit ，HEAD則指向 master。

```shell
PS D:\Project\Note\Git\git-demo> echo "dev" > dev.txt 
PS D:\Project\Note\Git\git-demo> ls 
目錄: D:\Project\Note\Git\git-demo
Mode LastWriteTime Length Name ---- ------------- ------ ---- d----- 6/30/2024 3:17 PM folder1 -a---- 7/4/2024 11:37 PM 12 dev.txt -a---- 6/30/2024 4:47 PM 11 file1.txt -a---- 6/30/2024 3:04 PM 18 file2.txt PS D:\Project\Note\Git\git-demo> git add dev.txt PS D:\Project\Note\Git\git-demo> git commit -m "1st commit from dev branch" [dev 79b9a47] 1st commit from dev branch 1 file changed, 0 insertions(+), 0 deletions(-) create mode 100644 dev.txt 
PS D:\Project\Note\Git\git-demo> git log commit 79b9a47d86a592d02e148a3cb08ac09d6fcfabe6 (HEAD -> dev) 
Author: taieeuu <taieeuu@gmail.com> 
Date: Thu Jul 4 23:37:32 2024 +0800 1st commit from dev branch commit abe59448e29b1b11d26a4876c0ec5e6fe8c3ea01 (master) 
Author: taieeuu <taieeuu@gmail.com> 
Date: Sun Jun 30 15:24:38 2024 +0800 3rd commit commit c59bdf3151a48b12ba79dd339cd50eca9cd5c120
Author: taieeuu <taieeuu@gmail.com> 
Date: Sun Jun 30 15:05:10 2024 +0800 2nd commit commit 041734cceb2ff014250a2cad2c762a631780fd30
Author: taieeuu <taieeuu@gmail.com> Date: Sun Jun 30 13:25:43 2024 +0800 1st commit PS D:\Project\Note\Git\git-demo>
```
>[!info]
>在 `dev` 分支上創建了一個新的文件 `dev.txt` 並提交了這個更改。現在 `dev` 分支有一個新的提交點 `79b9a47`，而 `master` 分支仍然停留在之前的提交點 `abe59448`。這表明你在 `dev` 分支上進行了修改，而 `master` 分支並沒有受到影響。

嘗試刪除 `dev` 分支：
```shell
git branch --delete dev
```
>[!info]
>嘗試刪除 `dev` 分支，但 Git 返回錯誤訊息，表示 `dev` 分支尚未完全合併。如果你確定要刪除這個分支，可以使用 `git branch -D` 強制刪除。


強制刪除 `dev` 分支：
```shell
git branch -D dev
```
>[!info]
>使用 `-D` 參數強制刪除 `dev` 分支，無論它是否合併。執行後顯示已刪除 `dev` 分支，並顯示該分支最後的提交點哈希值 `79b9a47`。

我們查看一下 `79b9a47` 提交類型
```shell
PS D:\Project\Note\Git\git-demo> git cat-file -t 79b9a
commit
```

查看提交內容：
```shell
PS D:\Project\Note\Git\git-demo> git cat-file -p 79b9a
tree 9e16e81c2eb519122eefd28386349f9ef2ce7439
parent abe59448e29b1b11d26a4876c0ec5e6fe8c3ea01
author taieeuu <taieeuu@gmail.com> 1720107452 +0800
committer taieeuu <taieeuu@gmail.com> 1720107452 +0800

1st commit from dev branch
```
>[!info]
>顯示提交 `79b9a` 的詳細內容，其中包括
>- `tree`：指向樹狀目錄 `9e16e81c2eb519122eefd28386349f9ef2ce7439`
>- `parent`：父提交 `abe59448e29b1b11d26a4876c0ec5e6fe8c3ea01`
>- `author` 和 `committer`：提交者的資訊
>- 提交訊息：`1st commit from dev branch`

查看樹狀目錄內容：
```shell
git cat-file -p 9e16e
```
>[!info]
>顯示樹狀目錄 `9e16e` 的內容，其中包括：
>- `dev.txt`：文件哈希 `488529d68b0f9b820559e821b750547165d6ed37`
>- `file1.txt`：文件哈希 `0ed2b0636232ff97fa8a3e9fa5f204e53711ff56`
>- `file2.txt`：文件哈希 `a405ac76aad26e6356e3651240930b0f38017c99`
>- `folder1`：資料夾哈希 `3528c7d661f2da27a2f37c5598787ba1408b5866`