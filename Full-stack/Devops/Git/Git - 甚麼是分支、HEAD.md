---
Date: 2024-07-04
tags:
  - devops
  - git
---
## 分支 (Branches)
- **分支是指向提交的命名指針**：
    - 分支在 Git 中是一個命名指針，用來指向特定的提交。
    - 分支的作用是幫助管理不同的工作線路和版本。
## Master 分支
- **Master 分支**：
    - Master 是一個分支，默認為主線分支。
    - 代表項目的主要開發線路，通常用於持有穩定的版本。
## HEAD 指針
- **HEAD 是一個特殊的指針**：
    - HEAD 指針總是指向當前活動分支的最新提交。
    - 它與當前活動分支相關，變更分支時，HEAD 也會隨之更新。
## Demo
1、**查看 HEAD 指針**：
```shell
PS D:\Project\Note\Git\git-demo> cat .git/HEAD
ref: refs/heads/master
```
- 顯示 HEAD 指針指向 refs/heads/master。

2、**查看 Master 分支指向的提交**：
```shell
PS D:\Project\Note\Git\git-demo> cat .git/refs/heads/master
abe59448e29b1b11d26a4876c0ec5e6fe8c3ea01
```
- 顯示 Master 分支指向的提交哈希值為 `abe59448e29b1b11d26a4876c0ec5e6fe8c3ea01`。

3、**確認提交類型**：
```shell
PS D:\Project\Note\Git\git-demo> git cat-file -t abe59
commit
```
- 確認哈希值 `abe59` 對應的物件類型是 `commit`。

4、**查看提交日誌**：
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
```
- 顯示目前 Master 分支的提交日誌，包括每個提交的哈希值、作者、日期和提交信息。

>[!info]
>也驗證確實當前最新的commit位於master分支的 abe59 。