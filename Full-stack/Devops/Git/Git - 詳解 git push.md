---
Date: 2024-07-14
tags:
  - git
  - devops
---
首先我們先在本地創建一個分支，接著修改並提交commit
```shell
PS D:\Project\Note\Git\test> git checkout -b feature-1
Switched to a new branch 'feature-1'
PS D:\Project\Note\Git\test> git branch -vv
  dev       407e180 [origin/dev] Merge branch 'dev' of https://github.com/taieeuu/test into dev
* feature-1 407e180 Merge branch 'dev' of https://github.com/taieeuu/test into dev
  main      50979e1 [origin/main] Update test.txt
PS D:\Project\Note\Git\test> mkdir feature-1


    目錄: D:\Project\Note\Git\test


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         7/14/2024  11:23 AM                feature-1
PS D:\Project\Note\Git\test\feature-1> echo "featur-1">feature-1.txt
```

從上述 `git branch -vv` 的輸出可以看到，當我們在本地創建一個分支時，並不會自動關聯至遠端，因為我們沒有告訴遠端我們創建了這個分支。此時，如果直接使用 `git push` 來推送，會失敗並顯示錯誤信息，提示我們需要指定要推送到的遠端分支。
而錯誤訊息告訴我們`feature-1`並沒有與遠端分支建立關聯，但可以看到當她有給提示說，可以使用`git push --set-upstream origin feature-1` 來進行關聯。
```shell
PS D:\Project\Note\Git\test> git push
fatal: The current branch feature-1 has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin feature-1

To have this happen automatically for branches without a tracking
upstream, see 'push.autoSetupRemote' in 'git help config'.
PS D:\Project\Note\Git\test> git push origin feature-1
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 16 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (4/4), 374 bytes | 374.00 KiB/s, done.
Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
remote:
remote: Create a pull request for 'feature-1' on GitHub by visiting:
remote:      https://github.com/taieeuu/test/pull/new/feature-1
remote:
To https://github.com/taieeuu/test.git
 * [new branch]      feature-1 -> feature-1
PS D:\Project\Note\Git\test> git branch -vv
  dev       407e180 [origin/dev] Merge branch 'dev' of https://github.com/taieeuu/test into dev
* feature-1 c8838ac add feature-1
  main      50979e1 [origin/main] Update test.txt
PS D:\Project\Note\Git\test>
```

通常`--set-upstream`並不常見，而是使用 `-u` 的參數來進行

我們先刪除遠端的分支，再進行 Demo ，可以看到遠端的 `feature-1` 已刪除。而本地的還存在。
```shell
PS D:\Project\Note\Git\test> git push origin -d feature-1
To https://github.com/taieeuu/test.git
 - [deleted]         feature-1
PS D:\Project\Note\Git\test> git branch -a
  dev
* feature-1
  main
  remotes/origin/HEAD -> origin/main
  remotes/origin/dev
  remotes/origin/main
PS D:\Project\Note\Git\test>
```

使用 `-u` 參數來進行推送，我們可以看到最後一條訊息本地的 `feature-1` 分支已經和 `origin/feature-1` 分支建立了追蹤關係，使用`git branch -vv`驗證確實已關聯。
```shell
PS D:\Project\Note\Git\test> git push -u origin feature-1
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 16 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (4/4), 374 bytes | 374.00 KiB/s, done.
Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
remote:
remote: Create a pull request for 'feature-1' on GitHub by visiting:
remote:      https://github.com/taieeuu/test/pull/new/feature-1
remote:
To https://github.com/taieeuu/test.git
 * [new branch]      feature-1 -> feature-1
branch 'feature-1' set up to track 'origin/feature-1'.
PS D:\Project\Note\Git\test> git branch -vv
  dev       407e180 [origin/dev] Merge branch 'dev' of https://github.com/taieeuu/test into dev
* feature-1 c8838ac [origin/feature-1] add feature-1
  main      50979e1 [origin/main] Update test.txt
PS D:\Project\Note\Git\test>
```

如果刪除`feature-1` 分支遇到錯誤，也可使用`-D`來進行強制刪除。