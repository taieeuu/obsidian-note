---
Date: 2024-06-30
tags:
  - git
  - devops
---
![[Drawing 2024-06-30 10.45.18.excalidraw.png]]
## .git/index
使用 `cat` 指令查看 `.git/index` 檔案的內容：
```shell
PS D:\Project\Note\Git\git-demo> cat .git/index
DIRCf薄}\f薄}\
?菸4~欒縜篤U?        file1.txt:Ut>CL芹+3u?
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>- `.git/index` 檔案包含 Git 的索引資訊，用於追蹤文件的狀態。

使用 `git ls-files` 查看 Git 管理的文件：
```shell
PS D:\Project\Note\Git\git-demo> git ls-files
file1.txt
```
>[!info]
>- 使用 `git ls-files` 指令可以列出目前 Git 管理的文件。

使用 `git ls-files -s` 查看詳細的文件索引資訊：
```shell
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 94468e38b5d29cc5347e8ac3f7e1ead4bf7755a4 0       file1.txt
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>- 使用 `git ls-files -s` 指令可以查看每個文件的詳細索引資訊，包括文件權限、Blob SHA-1 哈希值和文件路徑。
>	- 100644 : 就是為權限
>	- 94468e38b5d29cc5347e8ac3f7e1ead4bf7755a4 : 文件對應的 blob 對象，也就是 file1.txt 的內容 SHA1 
>	- 0 : 代表檔案在索引中的階段號。通常情況下，這個欄位的值為 `0`，表示這個檔案是普通的索引條目。這個欄位在處理合併衝突時會用到不同的值，但在沒有衝突的情況下，所有檔案的階段號都為 `0`。PS. 後面會詳細說明
>	- file1.txt : 文件名

```shell
PS D:\Project\Note\Git\git-demo> echo "file2">file2.txt
PS D:\Project\Note\Git\git-demo> ls


    目錄: D:\Project\Note\Git\git-demo


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         6/30/2024  10:23 AM             10 file1.txt
-a----         6/30/2024  10:59 AM             16 file2.txt


PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 94468e38b5d29cc5347e8ac3f7e1ead4bf7755a4 0       file1.txt
PS D:\Project\Note\Git\git-demo> git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   file1.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        file2.txt

PS D:\Project\Note\Git\git-demo>
```
>[!info]
>可以看到我們還沒有 `git add` 她會多了一個 Untracked 的狀態，且並沒有添加到我們的索引區。

添加至索引區
```

```shell
PS D:\Project\Note\Git\git-demo> git add .\file2.txt
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 94468e38b5d29cc5347e8ac3f7e1ead4bf7755a4 0       file1.txt
100644 077d212ad1d3a20e16ce13e2236bc179c470c1ad 0       file2.txt
PS D:\Project\Note\Git\git-demo> ls


    目錄: D:\Project\Note\Git\git-demo


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         6/30/2024  10:23 AM             10 file1.txt
-a----         6/30/2024  10:59 AM             16 file2.txt


PS D:\Project\Note\Git\git-demo> git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   file1.txt
        new file:   file2.txt

PS D:\Project\Note\Git\git-demo>
```

那如果說我們更改 file1.txt 會發生甚麼事呢 ?
```shell
PS D:\Project\Note\Git\git-demo> git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   file1.txt
        new file:   file2.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file1.txt

PS D:\Project\Note\Git\git-demo>
```

我們可以看到他的狀態被修改了，那他是如何知道的呢 ? 還記得在 .git/object/ 中有這個檔案的 SHA1 吧
```shell
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 94468e38b5d29cc5347e8ac3f7e1ead4bf7755a4 0       file1.txt
100644 077d212ad1d3a20e16ce13e2236bc179c470c1ad 0       file2.txt
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>由於第二個欄位代表的是檔案的內容的 SHA1 哈希值，所以他可以經由對比去發現檔案是否一致。

使用 `git add` 添加進索引區，發現她的哈希值確實變化。
```shell
PS D:\Project\Note\Git\git-demo> git add file1.txt
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 0ed2b0636232ff97fa8a3e9fa5f204e53711ff56 0       file1.txt
100644 077d212ad1d3a20e16ce13e2236bc179c470c1ad 0       file2.txt
PS D:\Project\Note\Git\git-demo>
```

```shell
PS D:\Project\Note\Git\git-demo\.git\objects>tree /F
├─objects
│  ├─07
│  │      7d212ad1d3a20e16ce13e2236bc179c470c1ad
│  │
│  ├─0e
│  │      d2b0636232ff97fa8a3e9fa5f204e53711ff56
│  │
│  ├─94
│  │      468e38b5d29cc5347e8ac3f7e1ead4bf7755a4
│  │
│  ├─info
│  └─pack
└─refs
    ├─heads
    └─tags
```
>[!info]
>從這個角度來看，我們會發現，他多了一個檔案，那這些都是 file1.txt 的歷史的檔案哈希值，那這樣 git commit 如何區分呢 ? 