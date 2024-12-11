---
Date: 2024-06-30
tags:
  - git
  - devops
---
## Git Commit
提交
```shell
PS D:\Project\Note\Git\git-demo> git commit -m "1st commit"
[master (root-commit) 041734c] 1st commit
 2 files changed, 1 insertion(+)
 create mode 100644 file1.txt
 create mode 100644 file2.txt
```
>[!info]
>這次提交操作創建了兩個新文件 `file1.txt` 和 `file2.txt`，並將它們添加到版本控制中。提交訊息是 `"1st commit"`，這次提交是儲存庫的第一次提交，因此被標記為根提交（root commit）。提交後的狀態顯示有兩個文件發生變更，並且有一次插入操作。這些資訊有助於我們了解提交的具體內容和影響的文件。
>- **git commit -m "1st commit"**
>	- 這個命令用來提交變更並附上提交訊息 `"1st commit"`。
>	- `-m` 參數用來指定提交訊息，避免進入編輯模式。
>- **[master (root-commit) 041734c] 1st commit**
>	- **master**：表示當前分支是 `master` 分支。
>	- **(root-commit)**：表示這是這個儲存庫的第一次提交，即根提交（root commit）。
>	- **041734c**：這是這次提交的 SHA-1 哈希值，唯一標識這次提交。
>	- **1st commit**：這是提交訊息。
>- **2 files changed, 1 insertion(+)**
>	- **2 files changed**：表示有兩個文件發生變更。
>	- **1 insertion(+)**：表示有一次插入操作（新增了一行內容）。
>- **create mode 100644 file1.txt**
>	- **create mode 100644**：表示創建了一個新的文件，文件權限為 `100644`，表示這是一個普通文件，所有者有讀寫權限，組和其他用戶只有讀權限。
>	- **file1.txt**：被創建的文件名稱。
>- **create mode 100644 file2.txt**
>	- 同樣地，**create mode 100644** 表示創建了一個新的文件，文件權限為 `100644`。
>	- **file2.txt**：被創建的另一個文件名稱。

查看提交物件的類型
```shell
PS D:\Project\Note\Git\git-demo> git cat-file -t 041734c
commit
```

查看提交物件的詳細內容
```shell
PS D:\Project\Note\Git\git-demo> git cat-file -p 041734c
tree 95b550dcd328b6868e2c65b9c8d70eadfaa4c5b7
author taieeuu <taieeuu@gmail.com> 1719725143 +0800
committer taieeuu <taieeuu@gmail.com> 1719725143 +0800

1st commit
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>使用 `git cat-file -p` 命令，我們可以查看每次提交的詳細資訊，包括目錄樹的哈希值、作者和提交者的資訊、時間戳和提交訊息。這些資訊有助於我們追踪版本控制歷史，了解每次變更的具體內容和背景。
>- **tree 95b550dcd328b6868e2c65b9c8d70eadfaa4c5b7**
>	- **tree** 表示這個提交指向的樹狀結構（目錄樹）的 SHA-1 哈希值。
>	- `95b550dcd328b6868e2c65b9c8d70eadfaa4c5b7` 是目錄樹的哈希值，用來表示這個提交包含的所有檔案及其目錄結構。
>- **author taieeuu taieeuu@gmail.com 1719725143 +0800**
>	- **author** 表示這個提交的作者。
>	- `taieeuu <taieeuu@gmail.com>` 是作者的名稱和電子郵件地址。
>	- `1719725143` 是作者提交這個變更的 Unix 時間戳，表示提交的具體時間。
>	- `+0800` 是時區偏移，表示提交時間相對於 UTC 的時區差。
>- **committer taieeuu taieeuu@gmail.com 1719725143 +0800**
>	- **committer** 表示這個提交的提交者，通常情況下和作者是一樣的。
>	- `taieeuu <taieeuu@gmail.com>` 是提交者的名稱和電子郵件地址。
>	- `1719725143` 是提交者提交這個變更的 Unix 時間戳。
>	- `+0800` 是時區偏移。
>- **1st commit**
>	- 這是提交訊息，描述了這次提交的內容或原因。在這個例子中，訊息是 "1st commit"。

我們查看了第一個提交（root commit）**041734c** 的 tree 目錄結構 **95b550dcd328b6868e2c65b9c8d70eadfaa4c5b7**。
```shell
PS D:\Project\Note\Git\git-demo> git cat-file -t 95b55 tree PS D:\Project\Note\Git\git-demo> git cat-file -p 95b55 100644 blob 0ed2b0636232ff97fa8a3e9fa5f204e53711ff56 file1.txt 100644 blob 077d212ad1d3a20e16ce13e2236bc179c470c1ad file2.txt PS D:\Project\Note\Git\git-demo>
```
>[!info]
>Ps. 每次提交（commit）都會生成一個新的目錄結構（tree）物件，即使文件內容沒有變更。這是因為每個提交（commit）物件都會指向一個新的目錄結構物件，其哈希值是基於該提交時的文件和目錄狀態計算出來的。因此，即使文件沒有變動，只要你進行了提交，目錄結構的哈希值也會不同
>- **git cat-file -t 95b55**
>	- **-t** 參數用來顯示指定哈希值（或部分哈希值）對應的物件類型。
>	- `95b55` 是目錄樹（tree）物件的哈希值部分。
>	- 輸出結果為 `tree`，表示這是一個目錄樹物件。
>- **git cat-file -p 95b55**
>	**-p** 參數用來顯示指定哈希值（或部分哈希值）對應的物件詳細內容。
>	- `95b55` 是目錄樹物件的哈希值部分。
>- **目錄樹物件的詳細內容**
>	- `100644 blob 0ed2b0636232ff97fa8a3e9fa5f204e53711ff56 file1.txt`
>	- **100644**：表示文件的權限，`100644` 表示這是一個普通文件，所有者有讀寫權限，組和其他用戶只有讀權限。
>- **blob**：表示這是一個 blob 物件，存儲文件的內容。
>	- `0ed2b0636232ff97fa8a3e9fa5f204e53711ff56`：這是文件 `file1.txt` 的 blob 物件的哈希值。
>- **file1.txt**：文件名。
>	- `100644 blob 077d212ad1d3a20e16ce13e2236bc179c470c1ad file2.txt`
>- **100644**：表示文件的權限，`100644` 表示這是一個普通文件，所有者有讀寫權限，組和其他用戶只有讀權限。
>- **blob**：表示這是一個 blob 物件，存儲文件的內容。
>	- `077d212ad1d3a20e16ce13e2236bc179c470c1ad`：這是文件 `file2.txt` 的 blob 物件的哈希值。
>- **file2.txt**：文件名。
## 其他變化檔案
1、**.git/objects/**
- 這個目錄包含所有的 Git 物件（objects），包括 blob、tree 和 commit 物件。
- 每次提交時，會新增新的物件到這個目錄中。

2、**.git/refs/heads/master**
- 這個檔案包含當前 `master` 分支指向的最新提交（commit）的 SHA-1 哈希值。
- 每次提交都會更新這個檔案，使其指向新的提交物件。
```shell
PS D:\Project\Note\Git\git-demo> cat .git/refs/heads/master
041734cceb2ff014250a2cad2c762a631780fd30
```
>[!info]
>那我們使用這個指令會發現這裡的 master 內容指向了041734cceb2ff014250a2cad2c762a631780fd30，這個也就是我們一開使提交的 root commit。

3、**.git/HEAD**
- 這個檔案指向當前所在的分支（例如 `refs/heads/master`）。
- 通常這個檔案不會因為新的提交而改變，除非你切換到不同的分支。
```shell
PS D:\Project\Note\Git\git-demo> cat .git/HEAD
ref: refs/heads/master
PS D:\Project\Note\Git\git-demo>
```

4、**.git/index**
- 這個檔案也被稱為暫存區（staging area）的索引（index）。
- 每次提交之後，這個檔案會被更新，反映當前已提交的狀態。
### 其他可能會變化的檔案和目錄
1. **.git/logs/HEAD**
    - 這個檔案記錄了 `HEAD` 的變化歷史。
    - 每次提交後，會新增一行記錄這次提交。
2. **.git/logs/refs/heads/master**
    - 這個檔案記錄了 `master` 分支的變化歷史。
    - 每次提交後，會新增一行記錄這次提交。
3. **.git/info/exclude**
    - 這個檔案包含忽略規則，類似於 `.gitignore`。
    - 如果這個檔案存在且被修改，提交時可能會包含變更。

ps. 詳細狀態之後會說明。
## 持續增加 Commit 來了解 Commit History Tree
新增文件並提交
```shell
PS D:\Project\Note\Git\git-demo> git add .\file2.txt
PS D:\Project\Note\Git\git-demo> git commit -m "2nd commit"
[master c59bdf3] 2nd commit
 1 file changed, 0 insertions(+), 0 deletions(-)
```
>[!info]
>- **git add .\file2.txt**
>	- 將 `file2.txt` 添加到暫存區。
>- **git commit -m "2nd commit"**
>	- 提交變更並附上提交訊息 "2nd commit"。
>	- 輸出顯示：這次提交的哈希值是 `c59bdf3`，分支是 `master`，提交訊息是 "2nd commit"。
>	- 有 1 個文件發生變更，但沒有插入或刪除操作。

查看第二次提交的詳細內容
```shell
PS D:\Project\Note\Git\git-demo> git cat-file -p c59bdf3
tree 19841f62d4d4bc001d9000d429e5c7dfa9dad3f6
parent 041734cceb2ff014250a2cad2c762a631780fd30
author taieeuu <taieeuu@gmail.com> 1719731110 +0800
committer taieeuu <taieeuu@gmail.com> 1719731110 +0800

2nd commit
```
>[!info]
>- **tree 19841f62d4d4bc001d9000d429e5c7dfa9dad3f6**
>	- 這是第二次提交的目錄結構（tree）物件的哈希值。
>- **parent 041734cceb2ff014250a2cad2c762a631780fd30**
>	- 這是上一個提交（第一次提交）的哈希值，表示這次提交是基於上一次提交的變更。
>- **author 和 committer**
>	- `author taieeuu <taieeuu@gmail.com> 1719731110 +0800`：作者資訊及提交時間。
>	- `committer taieeuu <taieeuu@gmail.com> 1719731110 +0800`：提交者資訊及提交時間。
>- **提交訊息**
>	- 提交訊息為 "2nd commit"。

查看第一次提交的詳細內容
```shell
PS D:\Project\Note\Git\git-demo> git cat-file -p 041734cc
tree 95b550dcd328b6868e2c65b9c8d70eadfaa4c5b7
author taieeuu <taieeuu@gmail.com> 1719725143 +0800
committer taieeuu <taieeuu@gmail.com> 1719725143 +0800

1st commit
```
>[!info]
>- **tree 95b550dcd328b6868e2c65b9c8d70eadfaa4c5b7**
>	- 這是第一次提交的目錄結構（tree）物件的哈希值。
>- **author 和 committer**
>	- `author taieeuu <taieeuu@gmail.com> 1719725143 +0800`：作者資訊及提交時間。
>	- `committer taieeuu <taieeuu@gmail.com> 1719725143 +0800`：提交者資訊及提交時間。
>- **提交訊息**
>	- 提交訊息為 "1st commit"。

所以說，我們每次 Commit 時，Tree 都會跟著變動。
我們目前的 Tree 為19841f62d4d4bc001d9000d429e5c7dfa9dad3f6，也會跟我們索引區的內容一致。
```shell
PS D:\Project\Note\Git\git-demo> git cat-file -p 19841
100644 blob 0ed2b0636232ff97fa8a3e9fa5f204e53711ff56    file1.txt
100644 blob a405ac76aad26e6356e3651240930b0f38017c99    file2.txt
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 0ed2b0636232ff97fa8a3e9fa5f204e53711ff56 0       file1.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       file2.txt
```
### 新增目錄及文件並查看狀態
```shell
PS D:\Project\Note\Git\git-demo> mkdir folder1
    目錄: D:\Project\Note\Git\git-demo

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         6/30/2024   3:16 PM                folder1


PS D:\Project\Note\Git\git-demo> git status
On branch master
nothing to commit, working tree clean
PS D:\Project\Note\Git\git-demo> cd .\folder1\
PS D:\Project\Note\Git\git-demo\folder1> echo "file2.">file3.txt
PS D:\Project\Note\Git\git-demo\folder1> git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        ./

nothing added to commit but untracked files present (use "git add" to track)
PS D:\Project\Note\Git\git-demo\folder1>
```
>[!info]
>我們可以由上述得知，建立一個新目錄時，Git 並不會自動追蹤或管理該目錄。只有當目錄中有文件被添加並被 Git 追蹤時，目錄才會被包含在版本控制中。

我們將上述 Add 一次，並查看暫存區內容
```shell
PS D:\Project\Note\Git\git-demo> git ls-files -s
100644 0ed2b0636232ff97fa8a3e9fa5f204e53711ff56 0       file1.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       file2.txt
100644 a405ac76aad26e6356e3651240930b0f38017c99 0       folder1/file3.txt
```
>[!info]
>你會發現為甚麼這邊的哈希值會一樣，而這是因為 file2.txt 中的內容與 folder1/file3.txt的內容是一樣的關係。

```shell
PS D:\Project\Note\Git\git-demo> git commit -m "3rd commit"
[master abe5944] 3rd commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 folder1/file3.txt
```
>[!info]
>我們會發現他在生成了三個新對象，其中一個一定是 Commit，而另外兩個一定是 Tree。

查看第三次提交的目錄結構
```shell
PS D:\Project\Note\Git\git-demo> git cat-file -p 500d4d0f1
100644 blob 0ed2b0636232ff97fa8a3e9fa5f204e53711ff56    file1.txt
100644 blob a405ac76aad26e6356e3651240930b0f38017c99    file2.txt
040000 tree 3528c7d661f2da27a2f37c5598787ba1408b5866    folder1
PS D:\Project\Note\Git\git-demo> git cat-file -p 3528c7d
100644 blob a405ac76aad26e6356e3651240930b0f38017c99    file3.txt
PS D:\Project\Note\Git\git-demo>
```
>[!info]
>藉此我們發現 Tree 裡面其實又包了一層 Tree 又包了一個 blob。就像目錄那樣的結構。
## 總結
每次提交後，除了更新或新增主要的 Git 物件外，一些記錄分支狀態和歷史的檔案也會發生變化。這些檔案和目錄的更新確保 Git 能夠有效地追蹤和管理每次提交的狀態和變更歷史。這使得我們能夠輕鬆地進行版本控制和回溯到之前的狀態。
### 架構圖
![[Drawing 2024-06-30 13.57.05.excalidraw.png]]