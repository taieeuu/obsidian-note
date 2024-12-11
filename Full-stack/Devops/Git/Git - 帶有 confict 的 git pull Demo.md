---
Date: 2024-07-14
tags:
  - git
  - devops
---
首先我們先與 Github 分支狀態全步同步，接著再修改 test.txt 檔案 (新增一條訊息)，並 `commit`，使用 `git branch -vv` 可以看到本地超前遠端一個 `commit` 。
```shell
PS D:\Project\Note\Git\test> code .
PS D:\Project\Note\Git\test> git add test.txt
PS D:\Project\Note\Git\test> git commit -m "update test.txt from local dev branch"
[dev 4b4828d] update test.txt from local dev branch
 1 file changed, 1 insertion(+)
PS D:\Project\Note\Git\test> git branch -vv
* dev  4b4828d [origin/dev: ahead 1] update test.txt from local dev branch
  main 50979e1 [origin/main] Update test.txt
PS D:\Project\Note\Git\test>
```

使用`git pull` 抓取遠程分支，並發現有衝突，打開vscode。
```shell
PS D:\Project\Note\Git\test> git pull -v
POST git-upload-pack (195 bytes)
POST git-upload-pack (368 bytes)
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 983 bytes | 327.00 KiB/s, done.
From https://github.com/taieeuu/test
   8f21f96..d737f4a  dev        -> origin/dev
 = [up to date]      main       -> origin/main
Auto-merging test.txt
CONFLICT (content): Merge conflict in test.txt
Automatic merge failed; fix conflicts and then commit the result.
PS D:\Project\Note\Git\test> code . 
```

接著我們可以根據自己需要去變更。再次提交即可
![[Pasted image 20240714104212.png]]

根據需要修改衝突部分的內容，確定最終版本後，移除衝突標記並保存文件。接著執行以下指令來完成合併
```shell
git add test.txt
git commit -m "Resolved merge conflict in test.txt"
```

這時會再產生一個 commit ，因為進行了 `merge`，接者推至 github
```shell
PS D:\Project\Note\Git\test> git branch -vv
* dev  407e180 [origin/dev: ahead 2] Merge branch 'dev' of https://github.com/taieeuu/test into dev
  main 50979e1 [origin/main] Update test.txt
PS D:\Project\Note\Git\test> git push origin dev
Enumerating objects: 10, done.
Counting objects: 100% (10/10), done.
Delta compression using up to 16 threads
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 642 bytes | 642.00 KiB/s, done.
Total 6 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), done.
To https://github.com/taieeuu/test.git
   d737f4a..407e180  dev -> dev
PS D:\Project\Note\Git\test>
```