---
Date: 2024-06-30
tags:
  - git
  - devops
---
## Git 的文件狀態
![[Drawing 2024-06-30 16.12.24.excalidraw.png]]
## 
創建一個 txt 文件，添加進索引區 (Stage)
```shell
PS D:\Project\Note\Git\git-demo> echo "test" > test.txt
PS D:\Project\Note\Git\git-demo> git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        test.txt

nothing added to commit but untracked files present (use "git add" to track)
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 0ed2b0636232ff97fa8a3e9fa5f204e53711ff56 0       file1.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       file2.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       folder1/file3.txt
PS D:\Project\Note\Git\git-demo> git add test.txt
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 0ed2b0636232ff97fa8a3e9fa5f204e53711ff56 0       file1.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       file2.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       folder1/file3.txt
100644 25b690689b298649c027af668c051282a96eed6c 0       test.txt
PS D:\Project\Note\Git\git-demo>
```

那如果我們想要凡毀呢 ? 
我們可以從 stage 刪除它
```shell
PS D:\Project\Note\Git\git-demo> git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   test.txt

PS D:\Project\Note\Git\git-demo> git rm --cached test.txt
rm 'test.txt'
PS D:\Project\Note\Git\git-demo> git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        test.txt

nothing added to commit but untracked files present (use "git add" to track)
PS D:\Project\Note\Git\git-demo>
```

但有時我們只是想返回前一個狀態，並不想要刪除該文件
```shell
PS D:\Project\Note\Git\git-demo> git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file1.txt

no changes added to commit (use "git add" and/or "git commit -a")
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 0ed2b0636232ff97fa8a3e9fa5f204e53711ff56 0       file1.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       file2.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       folder1/file3.txt
PS D:\Project\Note\Git\git-demo> git add .\file1.txt
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 e2129701f1a4d54dc44f03c93bca0a2aec7c5449 0       file1.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       file2.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       folder1/file3.txt
PS D:\Project\Note\Git\git-demo> git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   file1.txt

PS D:\Project\Note\Git\git-demo> git restore --staged .\file1.txt
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 0ed2b0636232ff97fa8a3e9fa5f204e53711ff56 0       file1.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       file2.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       folder1/file3.txt
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>我們從 git status 看見它給我們的提示可以使用`git restore --staged <file>`來將文件從索引區中移除（unstage），而不會刪除文件或其修改。這樣可以方便地管理哪些修改被包含在下一次提交中，而不影響工作目錄中的實際文件。

當我們想要丟棄工作目錄中的修改並恢復文件到上一次提交的狀態時，可以使用 `git restore <file>` 命令。這個命令會直接將文件還原到上一次提交的狀態，並且不會將其從索引區中移除。這樣可以方便地回退到穩定版本，清除不需要的修改。
```shell
PS D:\Project\Note\Git\git-demo> git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file1.txt

no changes added to commit (use "git add" and/or "git commit -a")
PS D:\Project\Note\Git\git-demo> git restore .\file1.txt
PS D:\Project\Note\Git\git-demo> git status
On branch master
nothing to commit, working tree clean
PS D:\Project\Note\Git\git-demo>
```
### 注意事項
- **慎用恢復命令**：使用 `git restore` 命令會丟棄未提交的修改，請確保這些修改真的不需要保存。
- **檢查狀態**：在執行 `git restore` 之前，使用 `git status` 檢查當前狀態，確保理解將要執行的操作。
- **備份重要修改**：在恢復之前，如果有重要的修改，請備份或提交這些修改以避免數據丟失。

