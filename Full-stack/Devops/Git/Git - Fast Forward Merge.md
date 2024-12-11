---
Date: 2024-07-06
tags:
  - git
  - devops
---
## Fast Forward Merge 工作原理
當你在功能分支（例如 `feature-branch`）上完成工作並想要合併回主分支（例如 `main`）時，如果在您創建 `feature-branch` 後，`main` 分支沒有新的提交，Git 可以直接將 `main` 分支的指針移動到 `feature-branch` 的最新提交，這樣的操作稱為 `Fast Forward Merge`。
![[Drawing 2024-07-06 15.12.19.excalidraw.png]]
## Demo
我們先初始化 Git ，且創建一個 commit ，來演示。
```shell
D:\Project\Note\Git\git-demo>git init
Initialized empty Git repository in D:/Project/Note/Git/git-demo/.git/

D:\Project\Note\Git\git-demo>git add .

D:\Project\Note\Git\git-demo>git commit -m "1st commit"
[master (root-commit) 40c5043] 1st commit
 1 file changed, 1 insertion(+)
 create mode 100644 test1.txt
```

假設今天有master中有一個 bug 需要修復，我們會在該分支切一支 `bugfix` 分支出去做修復，這邊也就是 master 分支要創建一個 bugfix 分支，然後作修復。
```shell
D:\Project\Note\Git\git-demo>git log
commit 40c5043036f45386721c8a34236a953310b4c4bc (HEAD -> master)
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 14:31:30 2024 +0800

    1st commit

D:\Project\Note\Git\git-demo>git checkout -b bugfix
Switched to a new branch 'bugfix'

D:\Project\Note\Git\git-demo>git log
commit 40c5043036f45386721c8a34236a953310b4c4bc (HEAD -> bugfix, master)
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 14:31:30 2024 +0800

    1st commit

D:\Project\Note\Git\git-demo>echo test2>test2.txt

D:\Project\Note\Git\git-demo>git add .

D:\Project\Note\Git\git-demo>git commit -m "2nd commit"
[bugfix bed0806] 2nd commit
 1 file changed, 1 insertion(+)
 create mode 100644 test2.txt
```

都修復完後，需要切回主分支做 `merge` 動作，但做這件事前我們來確認 `.git/` 資料夾是否正確，以及之後要查看與 `merge` 後產生了哪些的檔案：
```shell
D:\Project\Note\Git\git-demo\.git>tree /f
列出磁碟區 Data 的資料夾 PATH
磁碟區序號為 1C4C-DFEA
D:.
│  COMMIT_EDITMSG
│  config
│  description
│  HEAD
│  index
│
├─hooks
│      applypatch-msg.sample
│      commit-msg.sample
│      fsmonitor-watchman.sample
│      post-update.sample
│      pre-applypatch.sample
│      pre-commit.sample
│      pre-merge-commit.sample
│      pre-push.sample
│      pre-rebase.sample
│      pre-receive.sample
│      prepare-commit-msg.sample
│      push-to-checkout.sample
│      sendemail-validate.sample
│      update.sample
│
├─info
│      exclude
│
├─logs
│  │  HEAD
│  │
│  └─refs
│      └─heads
│              bugfix
│              master
│
├─objects
│  ├─18
│  │      0cf8328022becee9aaa2577a8f84ea2b9f3827
│  │
│  ├─40
│  │      c5043036f45386721c8a34236a953310b4c4bc
│  │
│  ├─81
│  │      fa9d1c5348f86326ce5e7a86d11b54c8140d8d
│  │
│  ├─a5
│  │      bce3fd2565d8f458555a0c6f42d0504a848bd5
│  │
│  ├─be
│  │      d08065c1bea249d4fe970980eb1103d1d78bea
│  │
│  ├─c0
│  │      da834e42dcbf7b2b1c4a97925bef105d3863a3
│  │
│  ├─info
│  └─pack
└─refs
    ├─heads
    │      bugfix
    │      master
    │
    └─tags
```
>[!info]
>確認有兩個分支 (bugfix 和 master)，並且有兩次 commit。每次 commit 都會產生三個對象：一個 commit、一個 blob、一個 tree。

接著，切回 master 分支，進行 `merge`：
```shell
D:\Project\Note\Git\git-demo\.git>cd ..

D:\Project\Note\Git\git-demo>git branch
  bugfix
* master

D:\Project\Note\Git\git-demo>git merge bugfix
Updating 40c5043..bed0806
Fast-forward
 test2.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 test2.txt

```
>[!info]
>

`merge` 成功後，比對一下多了一個 `ORIG_HEAD` 檔案：
```shell
D:\Project\Note\Git\git-demo\.git>tree /f
列出磁碟區 Data 的資料夾 PATH
磁碟區序號為 1C4C-DFEA
D:.
│  COMMIT_EDITMSG
│  config
│  description
│  HEAD
│  index
│  ORIG_HEAD
│
├─hooks
│      applypatch-msg.sample
│      commit-msg.sample
│      fsmonitor-watchman.sample
│      post-update.sample
│      pre-applypatch.sample
│      pre-commit.sample
│      pre-merge-commit.sample
│      pre-push.sample
│      pre-rebase.sample
│      pre-receive.sample
│      prepare-commit-msg.sample
│      push-to-checkout.sample
│      sendemail-validate.sample
│      update.sample
│
├─info
│      exclude
│
├─logs
│  │  HEAD
│  │
│  └─refs
│      └─heads
│              bugfix
│              master
│
├─objects
│  ├─18
│  │      0cf8328022becee9aaa2577a8f84ea2b9f3827
│  │
│  ├─40
│  │      c5043036f45386721c8a34236a953310b4c4bc
│  │
│  ├─81
│  │      fa9d1c5348f86326ce5e7a86d11b54c8140d8d
│  │
│  ├─a5
│  │      bce3fd2565d8f458555a0c6f42d0504a848bd5
│  │
│  ├─be
│  │      d08065c1bea249d4fe970980eb1103d1d78bea
│  │
│  ├─c0
│  │      da834e42dcbf7b2b1c4a97925bef105d3863a3
│  │
│  ├─info
│  └─pack
└─refs
    ├─heads
    │      bugfix
    │      master
    │
    └─tags
```
>[!info]
>注意到 `merge` 之後 `.git` 資料夾內多了一個 `ORIG_HEAD` 檔案。這個檔案用來保存 merge 前的 HEAD 的值，以便在需要的時候能夠回退。

來驗證一下，
```shell
PS D:\Project\Note\Git\git-demo> cat .git/HEAD
ref: refs/heads/master
PS D:\Project\Note\Git\git-demo> cat .git/ORIG_HEAD
40c5043036f45386721c8a34236a953310b4c4bc
PS D:\Project\Note\Git\git-demo> cat .git/refs/heads/master
bed08065c1bea249d4fe970980eb1103d1d78bea
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>`.git/HEAD` 檔案指向 `refs/heads/master`，表示當前分支是 master。 `.git/ORIG_HEAD` 檔案保存了 merge 前的 HEAD 的哈希值 `40c5043036f45386721c8a34236a953310b4c4bc`。 `.git/refs/heads/master` 檔案保存了 merge 後的最新 commit 的哈希值 `bed08065c1bea249d4fe970980eb1103d1d78bea`。
>與之前`merge` 時，**Updating 40c5043..bed0806** 相呼應，驗證無誤。

而在現實開發過程中，通常會與其他人合作開發。假設今天有個 bug 需要修復，創建了另一個分支進行修復。但在這期間，其他人可能也在開發他們的功能或修復他們的 bug，並且會在完成後直接 `merge` 回 master 分支，而不會等您完成修復，此時就不能符合 `fast forward merge` 這個條件了，那我們會在下一篇說明。