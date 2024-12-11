---
Date: 2024-07-14
tags:
  - devops
  - git
---
首先先在 Github 中創建一個 dev 分支與提交 file5.txt。

有偵測到有一個新分支
```shell
PS D:\Project\Note\Git\test> git remote show origin
* remote origin
  Fetch URL: https://github.com/taieeuu/test.git
  Push  URL: https://github.com/taieeuu/test.git
  HEAD branch: main
  Remote branches:
    dev  new (next fetch will store in remotes/origin)
    main tracked
  Local branch configured for 'git pull':
    main merges with remote main
  Local ref configured for 'git push':
    main pushes to main (local out of date)
PS D:\Project\Note\Git\test>
```

先使用`git gc` 壓縮 commit以及清除 smaple hooks，更方便展示
```SHELL
PS D:\Project\Note\Git\test> git gc
Enumerating objects: 13, done.
Counting objects: 100% (13/13), done.
Delta compression using up to 16 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (13/13), done.
Total 13 (delta 2), reused 10 (delta 2), pack-reused 0
PS D:\Project\Note\Git\test> rm .git/hooks/*.sample
```

然後我們從遠端倉庫拉取更新的分支信息與使用 `tree` 命令查看 `.git` 目錄結構，以確認更改是否生效，我們也可以發現實際上多了一個`FETCH_HEAD`這個文件。
```shell
PS D:\Project\Note\Git\test> git fetch
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 933 bytes | 311.00 KiB/s, done.
From https://github.com/taieeuu/test
 * [new branch]      dev        -> origin/dev
PS D:\Project\Note\Git\test> tree /F .git
列出磁碟區 Data 的資料夾 PATH
磁碟區序號為 1C4C-DFEA
D:\PROJECT\NOTE\GIT\TEST\.GIT
│  config
│  description
│  FETCH_HEAD
│  HEAD
│  index
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
│  ├─47
│  │      0c6537bd35aa21548b9c39e7fdc453c3d66be8
│  │
│  ├─7e
│  │      9621834f0921039e53f3212a114e9614303c30
│  │
│  ├─info
│  │      commit-graph
│  │      packs
│  │
│  └─pack
│          pack-a8c985bc3a631a64f935c345e933f8a0d01a7a37.idx
│          pack-a8c985bc3a631a64f935c345e933f8a0d01a7a37.pack
│          pack-a8c985bc3a631a64f935c345e933f8a0d01a7a37.rev
│
└─refs
    ├─heads
    ├─remotes
    │  └─origin
    │          dev
    │          HEAD
    │
    └─tags
```

檢視 `FETCH_HEAD`，並且你會看到其內容是: 顯示當前從遠端倉庫抓取的最新提交記錄
```shell
PS D:\Project\Note\Git\test> cat .git/FETCH_HEAD
50979e19e82c3b81e0f7ddc1a538c3c42b668440                branch 'main' of https://github.com/taieeuu/test
470c6537bd35aa21548b9c39e7fdc453c3d66be8        not-for-merge   branch 'dev' of https://github.com/taieeuu/test
PS D:\Project\Note\Git\test>
```

檢視所有分支，發現本地還未有`dev`分支，因為`dev`分支是在 Github 創建，在遠程有芬支的情況，我們可以使用`git branch dev`來創建同於`origin/dev`的commit 分支，使用`git branch -vv` 查看本地分支狀態。
```shell
PS D:\Project\Note\Git\test> git branch -a
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/dev
  remotes/origin/main
PS D:\Project\Note\Git\test> git branch -vv
* main 3510ad5 [origin/main: behind 1] Merge branch 'bugfix'
PS D:\Project\Note\Git\test> git checkout dev
Switched to a new branch 'dev'
branch 'dev' set up to track 'origin/dev'.
PS D:\Project\Note\Git\test> git branch -vv
* dev  470c653 [origin/dev] Create file5.txt
  main 3510ad5 [origin/main: behind 1] Merge branch 'bugfix'
```

我們可以發現，只要 `checkout` 到某個分支並進行 `fetch` 操作，`.git/FETCH_HEAD` 的內容就會變動。這是因為 `FETCH_HEAD` 記錄了最後一次從遠端抓取的提交信息。每次 `fetch` 操作都會更新這個記錄，以反映最新的遠端倉庫狀態。
```shell
PS D:\Project\Note\Git\test> cat .git/FETCH_HEAD
50979e19e82c3b81e0f7ddc1a538c3c42b668440                branch 'main' of https://github.com/taieeuu/test
470c6537bd35aa21548b9c39e7fdc453c3d66be8        not-for-merge   branch 'dev' of https://github.com/taieeuu/test
PS D:\Project\Note\Git\test> git checkout dev
PS D:\Project\Note\Git\test> git fetch
PS D:\Project\Note\Git\test> cat .git/FETCH_HEAD
470c6537bd35aa21548b9c39e7fdc453c3d66be8                branch 'dev' of https://github.com/taieeuu/test
50979e19e82c3b81e0f7ddc1a538c3c42b668440        not-for-merge   branch 'main' of https://github.com/taieeuu/test
PS D:\Project\Note\Git\test> git checkout main
PS D:\Project\Note\Git\test> git fetch
PS D:\Project\Note\Git\test> cat .git/FETCH_HEAD
50979e19e82c3b81e0f7ddc1a538c3c42b668440                branch 'main' of https://github.com/taieeuu/test
470c6537bd35aa21548b9c39e7fdc453c3d66be8        not-for-merge   branch 'dev' of https://github.com/taieeuu/test
PS D:\Project\Note\Git\test>
```