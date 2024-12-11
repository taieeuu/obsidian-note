---
Date: 2024-07-14
tags:
  - git
  - devops
---
## pull request 整體架構流程
1. **Fork 原始專案**：
2. **克隆（Clone）專案到本地**：
3. **創建新分支**
4. **進行修改並提交**
5. **推送修改到 GitHub**
6. **發起 Pull Request**
7. **等待原始專案的維護者審查**
8. **Pull Request 被合併**
## Demo
![[Pasted image 20240714203925.png]]

![[Pasted image 20240714204010.png]]

提交新的 commit，並 push 至遠端
```shell
PS D:\Project\Note\git-test> echo "add_pull_request_txt">add_pull_request.txt
PS D:\Project\Note\git-test> git add .
PS D:\Project\Note\git-test> git commit -m "add pull request txt"
[master d2bd73b] add pull request txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 add_pull_request.txt
PS D:\Project\Note\git-test> git push origin master
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 16 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 323 bytes | 323.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/taieeuu/git-test.git
   c545e37..d2bd73b  master -> master
PS D:\Project\Note\git-test>
```

此時會發現多了一個 Contribute 按鈕，點擊它後會看到「Open pull request」選項。這會告訴你要如何提交改動給原創作者：

點擊「Open pull request」後，你可以將你做的修改提交給原作者，請他檢查並決定是否合併到主分支中。
![[Pasted image 20240714205936.png]]

