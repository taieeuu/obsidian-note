---
Date: 2024-07-07
tags:
  - devops
  - git
---
這次我們來上傳代碼至 Github ，看看會發生什麼變化。

首先查看 `.git` 目錄下檔案會有什麼變化
```shell
PS D:\Project\Note\Git\git-demo> tree .git/
列出磁碟區 Data 的資料夾 PATH
磁碟區序號為 1C4C-DFEA
D:\PROJECT\NOTE\GIT\GIT-DEMO\.GIT
├─hooks
├─info
├─logs
│  └─refs
│      └─heads
├─objects
│  ├─0d
│  ├─18
│  ├─19
│  ├─1f
│  ├─25
│  ├─2a
│  ├─35
│  ├─96
│  ├─cd
│  ├─e7
│  ├─info
│  └─pack
└─refs
    ├─heads
    └─tags
PS D:\Project\Note\Git\git-demo> git remote add origin git@github.com:taieeuu/test.git
PS D:\Project\Note\Git\git-demo>
PS D:\Project\Note\Git\git-demo> tree .git/
列出磁碟區 Data 的資料夾 PATH
磁碟區序號為 1C4C-DFEA
D:\PROJECT\NOTE\GIT\GIT-DEMO\.GIT
├─hooks
├─info
├─logs
│  └─refs
│      └─heads
├─objects
│  ├─0d
│  ├─18
│  ├─19
│  ├─1f
│  ├─25
│  ├─2a
│  ├─35
│  ├─96
│  ├─cd
│  ├─e7
│  ├─info
│  └─pack
└─refs
    ├─heads
    └─tags
```
>[!info]
>當我們將本地倉庫與遠端倉庫連結起來後，透過執行 `git remote add origin <遠端倉庫地址>`，在 `.git` 目錄中並不會立即看到明顯的變化。因為此命令只是將遠端倉庫的地址資訊加入到本地倉庫中，並不會更改現有的檔案結構。

但在 `.git/config` 文件中添加有關遠端倉庫的信息。
```shell
PS D:\Project\Note\Git\git-demo> cat .git/config
[core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        symlinks = false
        ignorecase = true
[remote "origin"]
        url = git@github.com:xxx/test.git
        fetch = +refs/heads/*:refs/remotes/origin/*
PS D:\Project\Note\Git\git-demo>
```

然後就可以將我們的庫推至 github 了。
```shell
PS D:\Project\Note\Git\git-demo> git push -u origin main
Enumerating objects: 10, done.
Counting objects: 100% (10/10), done.
Delta compression using up to 16 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (10/10), 798 bytes | 798.00 KiB/s, done.
Total 10 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (2/2), done.
To https://github.com/xxx/test.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
PS D:\Project\Note\Git\git-demo>
```
### 命令和輸出解說
1. **Enumerating objects: 10, done.**
	- Git 開始列舉需要推送的物件，共有 10 個物件。
2. **Counting objects: 100% (10/10), done.** 
	- Git 完成了對這些物件的計數，共計 10 個。
3. **Delta compression using up to 16 threads**
	- Git 使用最多 16 個線程進行 Delta 壓縮，這是一種高效的數據壓縮技術，特別適合處理版本控制中的相似數據。
4. **Compressing objects: 100% (4/4), done.**
	- Git 完成了對 4 個物件的壓縮。
5. **Writing objects: 100% (10/10), 798 bytes | 798.00 KiB/s, done.**
	- Git 將壓縮後的 10 個物件寫入到遠端倉庫，總計 798 字節，傳輸速度為 798.00 KiB/s。
6. **Total 10 (delta 2), reused 0 (delta 0), pack-reused 0**
	- Git 總共傳輸了 10 個物件，其中包含 2 個 delta 壓縮的物件。這些物件是新傳輸的，而不是重用已有的 pack 文件。
7. **remote: Resolving deltas: 100% (2/2), done.**
	- 遠端倉庫完成了對 2 個 delta 壓縮物件的解析。
8. **To xxxx**
	- 這些物件已成功推送到 GitHub 遠端倉庫 `https://github.com/xxx/test.git`。
9. **[new branch] main -> main**
	- 新的分支 `main` 被推送到遠端倉庫，並且建立了對應的遠端分支 `origin/main`。
10. **branch 'main' set up to track 'origin/main'.**
	- 本地的 `main` 分支被設置為追蹤遠端的 `origin/main` 分支。這意味著未來在這個分支上的任何變更都可以通過 `git push` 和 `git pull` 來同步到遠端倉庫。
### 推送過程的詳細步驟
1. **建立連接**
    - Git 先與遠端倉庫建立連接，並檢查本地和遠端倉庫的狀態。
2. **列舉和計數物件**
    - Git 列舉出本地倉庫中需要推送的物件，並計數這些物件。
3. **壓縮物件**
    - 使用 Delta 壓縮技術將需要推送的物件壓縮，以減少傳輸過程中的數據量。
4. **傳輸物件**
    - 將壓縮後的物件傳輸到遠端倉庫。
5. **解析 Delta 壓縮**
    - 遠端倉庫解析接收到的 Delta 壓縮物件，並將其存儲在遠端倉庫中。
6. **更新遠端分支**
    - 更新遠端倉庫中的對應分支，並設置本地分支與遠端分支的追蹤關係。

然後使用 `tree .git/` 查看目前 .git 目錄狀況
```shell
PS D:\Project\Note\Git\git-demo> tree .git/
列出磁碟區 Data 的資料夾 PATH
磁碟區序號為 1C4C-DFEA
D:\PROJECT\NOTE\GIT\GIT-DEMO\.GIT
├─hooks
├─info
├─logs
│  └─refs
│      ├─heads
│      └─remotes
│          └─origin
├─objects
│  ├─0d
│  ├─18
│  ├─19
│  ├─1f
│  ├─25
│  ├─2a
│  ├─35
│  ├─96
│  ├─cd
│  ├─e7
│  ├─info
│  └─pack
└─refs
    ├─heads
    ├─remotes
    │  └─origin
    └─tags
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>在推送代碼至遠端倉庫後，我們發現 `.git` 目錄下的結構發生了一些變化，尤其是在 `logs` 和 `refs` 目錄中多了 remote 分支。

## 驗證
```shell
PS D:\Project\Note\Git\git-demo> cat .git/refs/remotes/origin/main
3510ad538920067fb7760824d1f6115d3d357482
PS D:\Project\Note\Git\git-demo> git cat-file -t 3510
commit
PS D:\Project\Note\Git\git-demo> cat .git/refs/heads/main
3510ad538920067fb7760824d1f6115d3d357482
PS D:\Project\Note\Git\git-demo> git log
commit 3510ad538920067fb7760824d1f6115d3d357482 (HEAD -> main, origin/main)
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

commit 1f94c0c9d28ee7f5f5aac39538128871b5c6b5a0 (bugfix)
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 20:19:28 2024 +0800

    2nd commit

commit 199b1e051ec58ace2ad241070632d596d47bf444
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 20:17:56 2024 +0800

    1st commit
PS D:\Project\Note\Git\git-demo>
```