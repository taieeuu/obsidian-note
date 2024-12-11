---
Date: 2024-06-29
tags:
  - git
  - devops
---
## Start
創建一個 `hello.txt` 在工作區
```shell
PS D:\Project\Note\Git\git-demo\.git> tree /F
列出磁碟區 Data 的資料夾 PATH
磁碟區序號為 1C4C-DFEA
D:.
│  config
│  description
│  HEAD
│  index
│
├─hooks
├─info
│      exclude
│
├─objects
│  ├─info
│  └─pack
└─refs
    ├─heads
    └─tags
```

當我們使用 `git add hello.txt` 後，會發現多了這些檔案，如下圖所示：
```shell
PS D:\Project\Note\Git\git-demo\.git> tree /F
列出磁碟區 Data 的資料夾 PATH
磁碟區序號為 1C4C-DFEA
D:.
│  config
│  description
│  HEAD
│  index
│
├─hooks
├─info
│      exclude
│
├─objects
│  ├─23
│  │      9f218d11ff40af4e3a65fb250009b6a1854011
│  │
│  ├─info
│  └─pack
└─refs
    ├─heads
    └─tags
```
>[!info]
>`git add` 還會更新以下文件和目錄：
>- `.git/HEAD`
>- `.git/index`
>- `.git/objects` 目錄下的相關對象
>像是 9f 這一串是哈希所編制而成，即為內容。

我們可以使用以下指令來查看它的類型，發現它顯示為 `blob`：
```shell
PS D:\Project\Note\Git\git-demo\.git> git cat-file -t 239f21
```
>[!info]
>這邊我們使用 -t 查看類型，`blob` 對象存儲文件的原始數據，當你添加文件到 Git 的暫存區時，Git 會創建一個 `blob` 對象來存儲這個文件的內容，並生成一個唯一的 SHA-1 哈希值作為這個 `blob` 對象的標識。

我們還可以使用以下指令來查看它的數據內容
```shell
git cat-file -p 239f21
```

而如果我們創建一個新的 TXT 檔案，且內容是一樣的話，我們會發現實際上它的哈希值並沒有改變，也就是說 Git 並沒有存儲新的文件副本，而是重用了相同的 `blob` 對象。這是因為 Git 使用內容地址存儲（content-addressable storage）技術，確保相同內容的文件只存儲一次。

但你使用 `git status` 會發現他是會有追蹤這一個新的檔案，也就代表他會在其他的地方去紀錄。



