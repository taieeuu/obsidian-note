---
Date: 2024-07-06
tags:
  - devops
  - git
---
## Demo
### 刪除分支
我們目前有三個分支，現在我們要刪除 `dev` 分支，如下
```shell
G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (master)
$ git branch
  dev
* master
  tmp

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (master)
$ git branch -d dev
error: the branch 'dev' is not fully merged
hint: If you are sure you want to delete it, run 'git branch -D dev'
hint: Disable this message with "git config advice.forceDeleteBranch false"

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (master)
$ git branch -D dev
Deleted branch dev (was 79b9a47).

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (master)
$
```
>[!info]
>我們可以使用`git branch -d <branch-name>` 來刪除分支，但是如果該 `branch` 沒有 `merge` 過的話，是無法刪除的，所以我們需要強制刪除，可使用 `-D` 來強制刪除。

### 查看刪除 branch 到底刪除了甚麼
```shell
G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (master)
$ find .git
.git
.git/COMMIT_EDITMSG
.git/config
.git/description
.git/HEAD
.git/hooks
.git/hooks/applypatch-msg.sample
.git/hooks/commit-msg.sample
.git/hooks/fsmonitor-watchman.sample
.git/hooks/post-update.sample
.git/hooks/pre-applypatch.sample
.git/hooks/pre-commit.sample
.git/hooks/pre-merge-commit.sample
.git/hooks/pre-push.sample
.git/hooks/pre-rebase.sample
.git/hooks/pre-receive.sample
.git/hooks/prepare-commit-msg.sample
.git/hooks/push-to-checkout.sample
.git/hooks/sendemail-validate.sample
.git/hooks/update.sample
.git/index
.git/info
.git/info/exclude
.git/logs
.git/logs/HEAD
.git/logs/refs
.git/logs/refs/heads
.git/logs/refs/heads/dev
.git/logs/refs/heads/master
.git/objects
.git/objects/04
.git/objects/04/1734cceb2ff014250a2cad2c762a631780fd30
.git/objects/07
.git/objects/07/7d212ad1d3a20e16ce13e2236bc179c470c1ad
.git/objects/0e
.git/objects/0e/d2b0636232ff97fa8a3e9fa5f204e53711ff56
.git/objects/19
.git/objects/19/841f62d4d4bc001d9000d429e5c7dfa9dad3f6
.git/objects/25
.git/objects/25/b690689b298649c027af668c051282a96eed6c
.git/objects/35
.git/objects/35/28c7d661f2da27a2f37c5598787ba1408b5866
.git/objects/48
.git/objects/48/8529d68b0f9b820559e821b750547165d6ed37
.git/objects/50
.git/objects/50/0d4d0f1cd7818534c4d0b0c019d547e67d99ef
.git/objects/79
.git/objects/79/b9a47d86a592d02e148a3cb08ac09d6fcfabe6
.git/objects/94
.git/objects/94/468e38b5d29cc5347e8ac3f7e1ead4bf7755a4
.git/objects/95
.git/objects/95/b550dcd328b6868e2c65b9c8d70eadfaa4c5b7
.git/objects/9e
.git/objects/9e/16e81c2eb519122eefd28386349f9ef2ce7439
.git/objects/a4
.git/objects/a4/05ac76aad26e6356e3651240930b0f38017c99
.git/objects/ab
.git/objects/ab/e59448e29b1b11d26a4876c0ec5e6fe8c3ea01
.git/objects/c5
.git/objects/c5/9bdf3151a48b12ba79dd339cd50eca9cd5c120
.git/objects/e2
.git/objects/e2/129701f1a4d54dc44f03c93bca0a2aec7c5449
.git/objects/info
.git/objects/pack
.git/packed-refs
.git/refs
.git/refs/heads
.git/refs/heads/dev
.git/refs/heads/master
.git/refs/tags
G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (master)
$ git branch -D dev
Deleted branch dev (was 79b9a47).

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (master)
$ find .git
.git
.git/COMMIT_EDITMSG
.git/config
.git/description
.git/HEAD
.git/hooks
.git/hooks/applypatch-msg.sample
.git/hooks/commit-msg.sample
.git/hooks/fsmonitor-watchman.sample
.git/hooks/post-update.sample
.git/hooks/pre-applypatch.sample
.git/hooks/pre-commit.sample
.git/hooks/pre-merge-commit.sample
.git/hooks/pre-push.sample
.git/hooks/pre-rebase.sample
.git/hooks/pre-receive.sample
.git/hooks/prepare-commit-msg.sample
.git/hooks/push-to-checkout.sample
.git/hooks/sendemail-validate.sample
.git/hooks/update.sample
.git/index
.git/info
.git/info/exclude
.git/logs
.git/logs/HEAD
.git/logs/refs
.git/logs/refs/heads
.git/logs/refs/heads/master
.git/objects
.git/objects/04
.git/objects/04/1734cceb2ff014250a2cad2c762a631780fd30
.git/objects/07
.git/objects/07/7d212ad1d3a20e16ce13e2236bc179c470c1ad
.git/objects/0e
.git/objects/0e/d2b0636232ff97fa8a3e9fa5f204e53711ff56
.git/objects/19
.git/objects/19/841f62d4d4bc001d9000d429e5c7dfa9dad3f6
.git/objects/25
.git/objects/25/b690689b298649c027af668c051282a96eed6c
.git/objects/35
.git/objects/35/28c7d661f2da27a2f37c5598787ba1408b5866
.git/objects/48
.git/objects/48/8529d68b0f9b820559e821b750547165d6ed37
.git/objects/50
.git/objects/50/0d4d0f1cd7818534c4d0b0c019d547e67d99ef
.git/objects/79
.git/objects/79/b9a47d86a592d02e148a3cb08ac09d6fcfabe6
.git/objects/94
.git/objects/94/468e38b5d29cc5347e8ac3f7e1ead4bf7755a4
.git/objects/95
.git/objects/95/b550dcd328b6868e2c65b9c8d70eadfaa4c5b7
.git/objects/9e
.git/objects/9e/16e81c2eb519122eefd28386349f9ef2ce7439
.git/objects/a4
.git/objects/a4/05ac76aad26e6356e3651240930b0f38017c99
.git/objects/ab
.git/objects/ab/e59448e29b1b11d26a4876c0ec5e6fe8c3ea01
.git/objects/c5
.git/objects/c5/9bdf3151a48b12ba79dd339cd50eca9cd5c120
.git/objects/e2
.git/objects/e2/129701f1a4d54dc44f03c93bca0a2aec7c5449
.git/objects/info
.git/objects/pack
.git/packed-refs
.git/refs
.git/refs/heads
.git/refs/heads/master
.git/refs/tags
```
>[!info]
>我們可以觀察到實際上的 commit 並沒有刪除，只有在 `.git/refs/heads` 跟 `.git/logs/refs/heads` 中的 dev 被刪除了 ! 


