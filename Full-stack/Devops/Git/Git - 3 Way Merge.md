---
Date: 2024-07-06
tags:
  - devops
  - git
---
![[Drawing 2024-07-06 15.12.19.excalidraw.png]]
繼上一篇，為了要 Demo 我們使用 `git reset` 回到了一開始 `commit` 的狀態，目前 Git 狀態。
```shell
PS D:\Project\Note\Git\git-demo> git reset ORIG_HEAD
PS D:\Project\Note\Git\git-demo> git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        test2.txt

nothing added to commit but untracked files present (use "git add" to track)
PS D:\Project\Note\Git\git-demo> git log
commit 40c5043036f45386721c8a34236a953310b4c4bc (HEAD -> master)
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 14:31:30 2024 +0800

    1st commit
PS D:\Project\Note\Git\git-demo>
```

在 master 分支上創建一個 `test3.txt` 文件並 commit。
```shell
PS D:\Project\Note\Git\git-demo> echo test3 > test3.txt
PS D:\Project\Note\Git\git-demo> git add test3.txt
PS D:\Project\Note\Git\git-demo> git commit -m "3rd commit"
[master 7a62847] 3rd commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test3.txt
PS D:\Project\Note\Git\git-demo> git log
commit 7a62847be935c25b2a775e221d54256e5263e630 (HEAD -> master)
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 15:56:24 2024 +0800

    3rd commit

commit 40c5043036f45386721c8a34236a953310b4c4bc
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 14:31:30 2024 +0800

    1st commit
PS D:\Project\Note\Git\git-demo>
```
![[Pasted image 20240706155800.png]]

合併回 master branch
```shell
PS D:\Project\Note\Git\git-demo> git merge bugfix
Merge made by the 'ort' strategy.
PS D:\Project\Note\Git\git-demo> git log
commit 15ce160da77dd45cfbce42eddc29286461ea4627 (HEAD -> master)
Merge: 0d6a76d bed0806
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 16:02:35 2024 +0800

    Merge branch 'bugfix'

commit 0d6a76dbd0a858179271aa5ee0a188e11ccf406c
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 16:02:13 2024 +0800

    3rd commit

commit 7a62847be935c25b2a775e221d54256e5263e630
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 15:56:24 2024 +0800

    3rd commit

commit bed08065c1bea249d4fe970980eb1103d1d78bea (bugfix)
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 14:32:27 2024 +0800

    2nd commit

commit 40c5043036f45386721c8a34236a953310b4c4bc
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 14:31:30 2024 +0800

    1st commit
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>主要看到合併後會自動創建一個合併 commit，標記為 `Merge branch 'bugfix'`，這樣在日誌中就能清楚地看到此次合併操作。此外，原來 `bugfix` 分支上的 commit 也保留在日誌中，完整記錄了該分支的變更歷史。

![[Pasted image 20240706161041.png]]

查看 `merge` 後，他幫我們生成的 `commit`。
```shell
PS D:\Project\Note\Git\git-demo> git cat-file -p  15ce160da77dd4
tree 2afe6e7444d36de1368c5ae4a7e59928205d43e3
parent 0d6a76dbd0a858179271aa5ee0a188e11ccf406c
parent bed08065c1bea249d4fe970980eb1103d1d78bea
author taieeuu <taieeuu@gmail.com> 1720252955 +0800
committer taieeuu <taieeuu@gmail.com> 1720252955 +0800

Merge branch 'bugfix'
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>- **tree 2afe6e7444d36de1368c5ae4a7e59928205d43e3**:
>	- 這一行指向了此 commit 所屬的樹對象（tree object）。樹對象包含了此 commit 的文件目錄結構和內容。
>- **parent 0d6a76dbd0a858179271aa5ee0a188e11ccf406c**:
>	- 這是此合併 commit 的第一個父 commit。這個 commit 是 master 分支的最新 commit，也就是合併之前 master 分支的狀態。
>- **parent bed08065c1bea249d4fe970980eb1103d1d78bea**:
>	- 這是此合併 commit 的第二個父 commit。這個 commit 是 bugfix 分支的最新 commit，也就是合併之前 bugfix 分支的狀態。

驗證
```shell
PS D:\Project\Note\Git\git-demo> cat .git/refs/heads/bugfix
bed08065c1bea249d4fe970980eb1103d1d78bea
PS D:\Project\Note\Git\git-demo> cat .git/ORIG_HEAD
0d6a76dbd0a858179271aa5ee0a188e11ccf406c
PS D:\Project\Note\Git\git-demo> cat .git/refs/heads/master
15ce160da77dd45cfbce42eddc29286461ea4627
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>- `cat .git/refs/heads/bugfix` : 這顯示了 `bugfix` 分支的最新 commit 是 `bed08065c1bea249d4fe970980eb1103d1d78bea`。
>- `cat .git/ORIG_HEAD` : 這一行的輸出顯示 `ORIG_HEAD` 文件的內容是 `0d6a76dbd0a858179271aa5ee0a188e11ccf406c`。`ORIG_HEAD` 用於保存上一次操作（例如合併、重置等）之前的 HEAD。這與之前查看的合併 commit 中的第一個父 commit 一致，即合併之前 `master` 分支的最新 commit。
>- `cat .git/refs/heads/master` : 這顯示了 `master` 分支的最新 commit 是 `15ce160da77dd45cfbce42eddc29286461ea4627`，即合併 commit。

**注意**
這邊的 `git log` ，由於是在 terminal 中，無法去做 分岔 的形式，所以有些限制。
所以通常我們都會使用可視化工具。
```shell
PS D:\Project\Note\Git\git-demo> git log
commit 15ce160da77dd45cfbce42eddc29286461ea4627 (HEAD -> master)
Merge: 0d6a76d bed0806
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 16:02:35 2024 +0800

    Merge branch 'bugfix'

commit 0d6a76dbd0a858179271aa5ee0a188e11ccf406c
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 16:02:13 2024 +0800

    3rd commit

commit 7a62847be935c25b2a775e221d54256e5263e630
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 15:56:24 2024 +0800

    3rd commit

commit bed08065c1bea249d4fe970980eb1103d1d78bea (bugfix)
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 14:32:27 2024 +0800

    2nd commit

commit 40c5043036f45386721c8a34236a953310b4c4bc
Author: taieeuu <taieeuu@gmail.com>
Date:   Sat Jul 6 14:31:30 2024 +0800

    1st commit
PS D:\Project\Note\Git\git-demo>
```

但在現實合作過程中，往往都會修改到同一個文件，而這時 `merge` 時，就會產生 `conflict` 衝突，也就會需要我們手動去修改。