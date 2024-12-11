---
Date: 2024-07-05
tags:
  - devops
  - git
---
## Demo
我們目前在 `abe5944` 這一支 commit 上，現在我們想要切到第一次 commit 的分支看看。
```shell
PS D:\Project\Note\Git\git-demo> git log
commit abe59448e29b1b11d26a4876c0ec5e6fe8c3ea01 (HEAD -> master)
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
PS D:\Project\Note\Git\git-demo> cat .git/HEAD
ref: refs/heads/master
PS D:\Project\Note\Git\git-demo> cat .git/refs/heads/master
abe59448e29b1b11d26a4876c0ec5e6fe8c3ea01
PS D:\Project\Note\Git\git-demo> git checkout 041734cceb
Note: switching to '041734cceb'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at 041734c 1st commit
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>我們使用 git log 查看我們想要到的特定 commit 的 `SHA1` 並使用`git checkout <commit_sha1>`，切過去後，他給了你一些 Note。
>現在處於 "detached HEAD" 狀態，這意味著你檢出了某個特定的提交（而不是分支的最新狀態）。在這個狀態下，你可以進行實驗性的更改，但這些更改並不會自動保存到任何分支。**如果你希望保存這些更改，應該創建一個新的分支**。

所以現在我們要從該 `commit` 切出去一支 `branch`，這邊主要有兩種方式，1、在該 `commit` 修改後，再開一個分支。2、先開分支，再修改。
```shell
G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo ((041734c...))
$ ls
file1.txt  file2.txt

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo ((041734c...))
$ git log
commit 041734cceb2ff014250a2cad2c762a631780fd30 (HEAD)
Author: taieeuu <taieeuu@gmail.com>
Date:   Sun Jun 30 13:25:43 2024 +0800

    1st commit

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo ((041734c...))
$ git checkout -b tmp
Switched to a new branch 'tmp'

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (tmp)
$ git log
commit 041734cceb2ff014250a2cad2c762a631780fd30 (HEAD -> tmp)
Author: taieeuu <taieeuu@gmail.com>
Date:   Sun Jun 30 13:25:43 2024 +0800

    1st commit

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (tmp)
$
```
>[!info]
>從 git log 可以看到，我們只有一個 commit ，因為剛剛切到了第一次的 commit，並使用 `git checkout -b tmp` 創建了另一個分支，查看 git log 後，HEAD確實是從 tmp 分支去做動作。

**注意**
在 git 操作上，我們進行分支的刪除又或是重置分支等時，他只是刪除了指向特定 commit 的指針而已，實際本身的 commit 並沒有被刪除。實際的 commit 仍然存在於 Git 的數據庫中，直到它們被 Git 的垃圾回收機制（通常是 `git gc` 命令）清理為止。

接下來，我們來復原之前所刪除的 branch ，我們可以使用 `git reflog` 來檢索記錄所有對分支的引用操作，這些記錄允許你在不小心執行錯誤操作時，找到分支先前的位置並進行恢復。
```shell
G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo ((abe5944...))
$ git reflog
abe5944 (HEAD, master) HEAD@{0}: checkout: moving from tmp to abe5944
041734c (tmp) HEAD@{1}: checkout: moving from 041734cceb2ff014250a2cad2c762a631780fd30 to tmp
041734c (tmp) HEAD@{2}: checkout: moving from master to 041734cceb
abe5944 (HEAD, master) HEAD@{3}: checkout: moving from dev to master
79b9a47 HEAD@{4}: commit: 1st commit from dev branch
abe5944 (HEAD, master) HEAD@{5}: checkout: moving from master to dev
abe5944 (HEAD, master) HEAD@{6}: commit: 3rd commit
c59bdf3 HEAD@{7}: commit: 2nd commit
041734c (tmp) HEAD@{8}: commit (initial): 1st commit

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo ((abe5944...))
$ git checkout 79b9a47
Previous HEAD position was abe5944 3rd commit
HEAD is now at 79b9a47 1st commit from dev branch

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo ((79b9a47...))
$ ls
dev.txt  file1.txt  file2.txt  folder1

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo ((79b9a47...))
$ git checkout -b dev
Switched to a new branch 'dev'

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (dev)
$ ls
dev.txt  file1.txt  file2.txt  folder1

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (dev)
$ git log
commit 79b9a47d86a592d02e148a3cb08ac09d6fcfabe6 (HEAD -> dev)
Author: taieeuu <taieeuu@gmail.com>
Date:   Thu Jul 4 23:37:32 2024 +0800

    1st commit from dev branch

commit abe59448e29b1b11d26a4876c0ec5e6fe8c3ea01 (master)
Author: taieeuu <taieeuu@gmail.com>
Date:   Sun Jun 30 15:24:38 2024 +0800

    3rd commit

commit c59bdf3151a48b12ba79dd339cd50eca9cd5c120
Author: taieeuu <taieeuu@gmail.com>
Date:   Sun Jun 30 15:05:10 2024 +0800

    2nd commit

commit 041734cceb2ff014250a2cad2c762a631780fd30 (tmp)
Author: taieeuu <taieeuu@gmail.com>
Date:   Sun Jun 30 13:25:43 2024 +0800

    1st commit

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (dev)
```
>[!info]
>我們恢復了之前刪除的 `dev` 分支，找到之前的 commit 再次創建 `dev` 分支。從操作中可以看到，實際上這些 commit 並沒有被刪除。

