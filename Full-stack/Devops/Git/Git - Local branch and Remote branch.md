---
Date: 2024-07-11
tags:
  - git
  - devops
---
我們接著上一個的專案繼續，已有在github上面。
![[Pasted image 20240711224143.png]]

### 查看分支狀態
```shell
PS D:\Project\Note\Git\test> git branch
* main
PS D:\Project\Note\Git\test> git branch -r
  origin/HEAD -> origin/main
  origin/main
PS D:\Project\Note\Git\test> git branch -a
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
PS D:\Project\Note\Git\test>
```
### 查看 .git 目錄
```shell
PS D:\Project\Note\Git\test> tree /F .git
列出磁碟區 Data 的資料夾 PATH
磁碟區序號為 1C4C-DFEA
D:\PROJECT\NOTE\GIT\TEST\.GIT
│  config
│  description
│  HEAD
│  index
│  packed-refs
.
.
.
├─logs
│  │  HEAD
│  │
│  └─refs
│      ├─heads
│      │      main
│      │
│      └─remotes
│          └─origin
│                  HEAD
│
├─objects
│  ├─info
│  └─pack
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.idx
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.pack
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.rev
│
└─refs
    ├─heads
    │      main
    │
    ├─remotes
    │  └─origin
    │          HEAD
    │
    └─tags
PS D:\Project\Note\Git\test>
```
>[!info]
>可以看到 remotes/origin/HEAD 中的分支沒有存在，那是因為 git 把他們壓縮在 `packed-refs` 文件中。

查看 `.git/packed-refs` 
```shell
PS D:\Project\Note\Git\test> cat .git/packed-refs
# pack-refs with: peeled fully-peeled sorted
3510ad538920067fb7760824d1f6115d3d357482 refs/remotes/origin/main
PS D:\Project\Note\Git\test>
```

查看 log
```shell
PS D:\Project\Note\Git\test> git log
commit 3510ad538920067fb7760824d1f6115d3d357482 (HEAD -> main, origin/main, origin/HEAD)
Merge: 0d72977 1f94c0c
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 21:10:35 2024 +0800

    Merge branch 'bugfix'

    # Conflicts:
    #       test.txt

commit 0d72977a30e0381a078b560b448e0593a6967d4a
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 20:20:18 2024 +0800

    3rd commit

commit 1f94c0c9d28ee7f5f5aac39538128871b5c6b5a0
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 20:19:28 2024 +0800

    2nd commit

commit 199b1e051ec58ace2ad241070632d596d47bf444
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 20:17:56 2024 +0800

    1st commit
PS D:\Project\Note\Git\test>
```
>[!info]
>可以看到 本地的HEAD指向main, origin/main, origin/HEAD，同為 main 分支。但要注意的是 ` origin/main` 實際上是儲存在我們本地訊息，那這個訊息是我們進行代碼 clone 時所指向的 commit。

那實際在公司中，會有許多的合作，主代碼不一定 `clone` 下來往後都是這個版本，一定還會有人 `push` 更新分支的，那這時我們可以更新我們的遠程分支。
```shell
git fetch
```

## Git Fetch 後檢查 `.git` 資料夾中的 `packed-refs` 和 `refs/remotes/origin/main` 的更新情況
檢查 `.git/packed-refs` 文件內容
```shell
PS D:\Project\Note\Git\test> cat .git/packed-refs
# pack-refs with: peeled fully-peeled sorted
3510ad538920067fb7760824d1f6115d3d357482 refs/remotes/origin/main
```
>[!info]
>此處為壓縮後的 `remote` 分支的 SHA-1 值。`packed-refs` 文件包含了所有引用（refs），這些引用已被壓縮以提高查找效率。

**補充**
`refs` 是指引用 (references)，它們用來指向特定的提交 (commit)。以下是一些常見的 `refs`：
1. **分支引用 (Branch References)**：指向某個分支的最新提交。例如，`refs/heads/main` 代表 `main` 分支的最新提交。
2. **遠端引用 (Remote References)**：指向遠端倉庫中的分支。例如，`refs/remotes/origin/main` 代表遠端倉庫 `origin` 中 `main` 分支的最新提交。
3. **標籤引用 (Tag References)**：指向某個特定提交的標籤。例如，`refs/tags/v1.0` 代表標籤 `v1.0` 所指向的提交。

```shell
PS D:\Project\Note\Git\test> cat .git/packed-refs
# pack-refs with: peeled fully-peeled sorted
3510ad538920067fb7760824d1f6115d3d357482 refs/remotes/origin/main
PS D:\Project\Note\Git\test> git fetch
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 934 bytes | 311.00 KiB/s, done.
From https://github.com/taieeuu/test
   3510ad5..50979e1  main       -> origin/main
PS D:\Project\Note\Git\test> cat .git/packed-refs
# pack-refs with: peeled fully-peeled sorted
3510ad538920067fb7760824d1f6115d3d357482 refs/remotes/origin/main
PS D:\Project\Note\Git\test>
```
>[!info]
>- `git fetch` 從遠端倉庫獲取最新的更新。
>- 遠端倉庫的 `main` 分支有新的提交，SHA-1 哈希值從 `3510ad5` 更新到 `50979e1`。
>- 即使執行 `git fetch` 後，`packed-refs` 文件中的 SHA-1 哈希值未改變，仍然是 `3510ad538920067fb7760824d1f6115d3d357482`。
#### 補充說明
- `git fetch` 從遠端倉庫獲取更新後，會更新本地的引用（refs），但並不會自動更新工作區的代碼。這意味著雖然遠端的提交已經獲取，但本地的代碼還沒有實際應用這些更新。
- 要將這些更新應用到本地的代碼，可以使用 `git merge` 或 `git rebase` 命令，將遠端分支的變更合併到本地分支。
- 這邊的遠程倉庫為 : `.git/refs/remotes`。

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
│  packed-refs
│
.
.
.
├─info
│      exclude
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
│                  HEAD
│                  main
│
├─objects
│  ├─0e
│  │      d13e7f9f27febde83c47883353c519fa2b7309
│  │
│  ├─10
│  │      2156b3ad2f3594903d26f9be140279e0b8a693
│  │
│  ├─50
│  │      979e19e82c3b81e0f7ddc1a538c3c42b668440
│  │
│  ├─info
│  └─pack
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.idx
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.pack
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.rev
│
└─refs
    ├─heads
    │      main
    │
    ├─remotes
    │  └─origin
    │          HEAD
    │          main
    │
    └─tags
PS D:\Project\Note\Git\test>
```
>[!info]
>- `remotes/origin/` 底下出現了 `main` 分支，這代表該分支已經更新為最新的狀態。因為這是 `fetch` 完後的。
>- `remotes/origin/main` 文件中的 SHA-1 哈希值已更新。

```shell
PS D:\Project\Note\Git\test> cat .git/refs/remotes/origin/main
50979e19e82c3b81e0f7ddc1a538c3c42b668440
PS D:\Project\Note\Git\test> cat .git/refs/remotes/origin/HEAD
ref: refs/remotes/origin/main
PS D:\Project\Note\Git\test> cat .git/refs/remotes/origin/main
50979e19e82c3b81e0f7ddc1a538c3c42b668440
PS D:\Project\Note\Git\test> cat .git/packed-refs
# pack-refs with: peeled fully-peeled sorted
3510ad538920067fb7760824d1f6115d3d357482 refs/remotes/origin/main
PS D:\Project\Note\Git\test>
```
>[!info]
當你執行 `git fetch` 之後：
>- `.git/refs/remotes/origin/main` 文件更新為遠程 `main` 分支的最新提交哈希值。
>- `.git/refs/remotes/origin/HEAD` 文件仍然指向 `refs/remotes/origin/main`，表明 `origin` 的默認分支是 `main`。
>- `.git/packed-refs` 文件可能包含了之前的引用狀態，顯示上一次 `fetch` 之前 `refs/remotes/origin/main` 的狀態

## 在 github 上創建分支，fetch 情況
先使用 github 創建一個 dev 分支
![[Pasted image 20240713162310.png]]

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
PS D:\Project\Note\Git\test> git fetch
From https://github.com/taieeuu/test
 * [new branch]      dev        -> origin/dev
PS D:\Project\Note\Git\test> git remote show origin
* remote origin
  Fetch URL: https://github.com/taieeuu/test.git
  Push  URL: https://github.com/taieeuu/test.git
  HEAD branch: main
  Remote branches:
    dev  tracked
    main tracked
  Local branch configured for 'git pull':
    main merges with remote main
  Local ref configured for 'git push':
    main pushes to main (local out of date)
PS D:\Project\Note\Git\test> git branch -r
  origin/HEAD -> origin/main
  origin/dev
  origin/main
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
│  ├─0e
│  │      d13e7f9f27febde83c47883353c519fa2b7309
│  │
│  ├─10
│  │      2156b3ad2f3594903d26f9be140279e0b8a693
│  │
│  ├─50
│  │      979e19e82c3b81e0f7ddc1a538c3c42b668440
│  │
│  ├─info
│  └─pack
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.idx
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.pack
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.rev
│
└─refs
    ├─heads
    │      main
    │
    ├─remotes
    │  └─origin
    │          dev
    │          HEAD
    │          main
    │
    └─tags
PS D:\Project\Note\Git\test> cat .git/refs/remotes/origin/HEAD
ref: refs/remotes/origin/main
```
>[!info]
>使用 `git remote show origin` 查看遠程分支狀態，發現有新分支 `dev`。接著使用 `git fetch` 更新遠程分支，更新後再次使用 `git remote show origin` 查看，確認新分支已經被追蹤。
>驗證 `git branch -r` 以查看遠程分支列表，確認新分支 `origin/dev` 已經出現。最後使用 `tree .git/` 命令檢查 `.git` 目錄結構，在 `refs/remotes/origin` 資料夾中可以看到 `dev` 分支。

## Github 刪除分支，並 fetch 情況
首先從 Github 刪除 dev 分支。

實際上，我們在 terminal 查看時是無法立即得知該分支是否已被刪除的，因為我們尚未同步遠端資訊到本地。
```shell
PS D:\Project\Note\Git\test> git branch -r
  origin/HEAD -> origin/main
  origin/dev
  origin/main
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
│  ├─0e
│  │      d13e7f9f27febde83c47883353c519fa2b7309
│  │
│  ├─10
│  │      2156b3ad2f3594903d26f9be140279e0b8a693
│  │
│  ├─50
│  │      979e19e82c3b81e0f7ddc1a538c3c42b668440
│  │
│  ├─info
│  └─pack
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.idx
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.pack
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.rev
│
└─refs
    ├─heads
    │      main
    │
    ├─remotes
    │  └─origin
    │          dev
    │          HEAD
    │          main
    │
    └─tags
```

現在我們需要將遠端的變更同步到本地。使用 `git fetch` 發現無法有效同步，因為 `git fetch` 只能同步新增的分支，無法辨識到已刪除的分支。
```shell
PS D:\Project\Note\Git\test> git fetch
PS D:\Project\Note\Git\test> git branch -r
  origin/HEAD -> origin/main
  origin/dev
  origin/main
```

顯示遠端倉庫詳細資訊的 Git 命令，這邊有提示說可以使用`git remote prune`來清除舊分支，那實際上我們也可以使用`git fetch` 只不過需要加上 `--prune` 參數。
```shell
PS D:\Project\Note\Git\test> git remote show origin
* remote origin
  Fetch URL: https://github.com/taieeuu/test.git
  Push  URL: https://github.com/taieeuu/test.git
  HEAD branch: main
  Remote branches:
    main                    tracked
    refs/remotes/origin/dev stale (use 'git remote prune' to remove)
  Local branch configured for 'git pull':
    main merges with remote main
  Local ref configured for 'git push':
    main pushes to main (local out of date)
PS D:\Project\Note\Git\test> git fetch --prune
From https://github.com/taieeuu/test
 - [deleted]         (none)     -> origin/dev
```

查看 .git 目錄結構
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
│  packed-refs
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
│      ├─heads
│      │      main
│      │
│      └─remotes
│          └─origin
│                  HEAD
│                  main
│
├─objects
│  ├─0e
│  │      d13e7f9f27febde83c47883353c519fa2b7309
│  │
│  ├─10
│  │      2156b3ad2f3594903d26f9be140279e0b8a693
│  │
│  ├─50
│  │      979e19e82c3b81e0f7ddc1a538c3c42b668440
│  │
│  ├─info
│  └─pack
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.idx
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.pack
│          pack-b9dd299e8373529a375b1f23343bb5b38ecca5b4.rev
│
└─refs
    ├─heads
    │      main
    │
    ├─remotes
    │  └─origin
    │          HEAD
    │          main
    │
    └─tags
```
>[!info]
>顯示 .git 目錄的完整結構，讓你可以檢查本地 Git 倉庫的內部狀況。

**補充**
列出所有本地分支，並顯示它們的詳細信息，包括每個分支的最新提交、提交信息，以及追蹤的遠端分支（如果有）。
```SHELL
git branch -vv
```
>[!info]
>- **`-v`**：顯示每個分支的最新提交的簡短摘要（commit hash 和提交信息）。
>- **`-vv`**：除了顯示最新提交的摘要，還顯示每個本地分支追蹤的遠端分支（如果有），並標明分支是否與遠端分支同步。