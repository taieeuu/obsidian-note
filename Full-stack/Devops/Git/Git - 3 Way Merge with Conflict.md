---
Date: 2024-07-06
tags:
  - devops
  - git
---
![[Drawing 2024-07-06 20.21.19.excalidraw.png]]

>[!info]
>從原始的 C1 commit 的 master 分支，切了一個 bugfix 分支出來，然後對同一個文件進行操作，在 `merge` 回 master 分支，此時會遇到 conflict 。

## Demo
合併時，發生衝突，因為兩支分支都對同一個文件進行操作，git 不知道該聽誰的，所以需要手動解決才能 `merge`。
```shell
PS D:\Project\Note\Git\git-demo> git merge bugfix
warning: Cannot merge binary files: test.txt (HEAD vs. bugfix)
Auto-merging test.txt
CONFLICT (content): Merge conflict in test.txt
Automatic merge failed; fix conflicts and then commit the result.
PS D:\Project\Note\Git\git-demo>
```

此時我們查看一下目前的狀態
```shell
PS D:\Project\Note\Git\git-demo> git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   test.txt

no changes added to commit (use "git add" and/or "git commit -a")
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>此時，Git 告訴你在 `test.txt` 文件中存在合併衝突，這是由於該文件在兩個分支中都進行了修改。
### 檢查文件中的衝突標記
如果說你 cat 該文件會發現多了一些這樣的內容，代表這是衝突，我們需要手動去更改
```shell
<<<<<<<<<<HEAD
....
========
.....
>>>>>>>>>>bugfix
```
>[!info]
>這些標記表示文件在兩個分支中的不同版本，需要手動進行合併。
### 查看文件狀態
你可以使用 `git ls-files -s` 命令來檢查文件的狀態：
```shell
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 25b690689b298649c027af668c051282a96eed6c 1       test.txt
100644 966147b3f00d299346b4c45889f8fa98a2a23570 2       test.txt
100644 1845db9496a64908cfc16803996168e3bbe4f020 3       test.txt
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>這表示 `test.txt` 文件存在三個不同版本的 blob，其中：
>- `1` 表示 `HEAD` 版本（當前分支）
>- `2` 表示合併進來的版本（`bugfix` 分支）
>- `3` 表示共同的基準版本
### 合併完後
要記得 `git add` 與 `git commit`，而這時 commit 會出現預設好的訊息，Git 會打開一個文本編輯器（通常是 `vim` 或你配置的其他編輯器），按照編輯器的保存和退出命令保存提交訊息並退出。對於 `vim`，按 `ESC` 鍵，輸入 `:wq`，然後按 `Enter` 鍵。

```shell
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 966147b3f00d299346b4c45889f8fa98a2a23570 0       test.txt
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>這時指會有一個文件，這表示文件已經被成功合併且沒有未解決的衝突。這個結果意味著所有衝突已經解決並且文件已經被添加到索引中準備提交。



