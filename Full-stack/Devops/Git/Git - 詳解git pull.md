---
Date: 2024-07-14
tags:
  - devops
  - git
---
首先查看了當前的分支狀態，然後從遠端倉庫抓取最新的提交記錄，是否保持同步。
```shell
PS D:\Project\Note\Git\test> git branch -a
  dev
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/dev
  remotes/origin/main
PS D:\Project\Note\Git\test> git fetch -v
POST git-upload-pack (196 bytes)
From https://github.com/taieeuu/test
 = [up to date]      main       -> origin/main
 = [up to date]      dev        -> origin/dev
PS D:\Project\Note\Git\test> git branch -vv
  dev  470c653 [origin/dev] Create file5.txt
* main 3510ad5 [origin/main: behind 1] Merge branch 'bugfix'
PS D:\Project\Note\Git\test> git remote show origin
* remote origin
  Fetch URL: https://github.com/taieeuu/test.git
  Push  URL: https://github.com/taieeuu/test.git
  HEAD branch: main
  Remote branches:
    dev  tracked
    main tracked
  Local branches configured for 'git pull':
    dev  merges with remote dev
    main merges with remote main
  Local refs configured for 'git push':
    dev  pushes to dev  (up to date)
    main pushes to main (local out of date)
PS D:\Project\Note\Git\test>
```

我們首先檢視了當前的 FETCH_HEAD，然後執行 `git pull` 操作，拉取並合併了來自遠端倉庫的最新變更。這次操作更新了 `dev` 分支的提交記錄，並且 `.git/FETCH_HEAD` 文件的內容也隨之變更，顯示最新的提交信息。
```shell
PS D:\Project\Note\Git\test> cat .git/FETCH_HEAD
50979e19e82c3b81e0f7ddc1a538c3c42b668440                branch 'main' of https://github.com/taieeuu/test
470c6537bd35aa21548b9c39e7fdc453c3d66be8        not-for-merge   branch 'dev' of https://github.com/taieeuu/test
PS D:\Project\Note\Git\test> git pull -v
POST git-upload-pack (195 bytes)
POST git-upload-pack (268 bytes)
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 938 bytes | 312.00 KiB/s, done.
From https://github.com/taieeuu/test
   470c653..8f21f96  dev        -> origin/dev
 = [up to date]      main       -> origin/main
Updating 470c653..8f21f96
Fast-forward
 file5.txt | 1 +
 1 file changed, 1 insertion(+)
PS D:\Project\Note\Git\test> cat .git/FETCH_HEAD
8f21f96e01855ead0a16e6ffe2adb58cde8783ae                branch 'dev' of https://github.com/taieeuu/test
50979e19e82c3b81e0f7ddc1a538c3c42b668440        not-for-merge   branch 'main' of https://github.com/taieeuu/test
PS D:\Project\Note\Git\test>
```
>[!info]
>- POST git-upload-pack (195 bytes) : 至 Updating 為git fetch
>- Updating 470c653..8f21f96 : 以下為 git merge

由於我們 `pull` 完，我們查看 `.git` 目錄有什麼變化。此外，我們發現 `.git` 目錄中多了一個 `ORIG_HEAD` 檔案。這個檔案用來記錄合併前的頭指針位置，以便在需要時可以恢復到之前的狀態。
```shell
PS D:\Project\Note\Git\test> tree /F .git
列出磁碟區 Data 的資料夾 PATH
磁碟區序號為 1C4C-DFEA
D:\PROJECT\NOTE\GIT\TEST\.GIT
│  config
│  description
│  FETCH_HEAD
│  HEAD
│  index
│  ORIG_HEAD
│  packed-refs
│
├─hooks
├─info
│      exclude
│      refs
│
├─logs
│  │  HEAD
│  │
│  └─refs
│      ├─heads
│      │      dev
│      │      main
│      │
│      └─remotes
│          └─origin
│                  dev
│                  HEAD
│                  main
│
├─objects
│  ├─31
│  │      bc5891683c2e87f7457b650dc82050c1b8f706
│  │
...
└─refs
    ├─heads
    │      dev
    │
    ├─remotes
    │  └─origin
    │          dev
    │          HEAD
    │
    └─tags
```

查看 `ORIG_HEAD`
```shell
PS D:\Project\Note\Git\test> cat .git/ORIG_HEAD
470c6537bd35aa21548b9c39e7fdc453c3d66be8
```
>[!info]
>在執行 `git pull` 指令之後，`.git/ORIG_HEAD` 文件會記錄更新前的 commit hash 值。從上面的結果來看，`470c6537bd35aa21548b9c39e7fdc453c3d66be8` 是在進行合併前的 commit hash，這可以對應於 `git pull` 的詳細訊息中的 `Updating 470c653..8f21f96`，表示從 `470c653` 更新到 `8f21f96`。

使用 `git reset --hard ORIG_HEAD` 回退到該 commit，也就是上述剛剛執行的 `git pull` 的 `merge` 被取消了，回到上一個 `commit`，也就是 `470c653`。使用 `git branch -vv` 也可以看到 `dev` 分支落後 `origin/dev` 一個 `commit`。
```shell
PS D:\Project\Note\Git\test> git reset --hard ORIG_HEAD
HEAD is now at 470c653 Create file5.txt
PS D:\Project\Note\Git\test> git branch -vv
* dev  470c653 [origin/dev: behind 1] Create file5.txt
  main 3510ad5 [origin/main: behind 1] Merge branch 'bugfix'
PS D:\Project\Note\Git\test>
```