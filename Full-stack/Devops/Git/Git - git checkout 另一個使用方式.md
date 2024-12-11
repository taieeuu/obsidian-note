---
Date: 2024-07-06
tags:
  - git
  - devops
---
當我們修改了一個文件，使用 `git status` 會出現 modified 的這個狀態，那其實他會出現提示告訴你下一步需要做甚麼
```shell
G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   Readme.md

no changes added to commit (use "git add" and/or "git commit -a")

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (master)
$
```
>[!info]
>- 使用 `git add` 指令來暫存這些修改，以便將它們包括在下一次提交中。
>- 使用 `git restore <file>` 指令來丟棄工作目錄中的修改，恢復文件到最新的提交狀態。

在之前的版本，我們可以使用`git checkout`來丟棄工作目錄中的修改，恢復文件到最新的提交狀態。
### `git checkout Readme.md`
- **用途**：將 `Readme.md` 文件恢復到 HEAD（最近一次提交）的狀態。
- **命令**：`git checkout Readme.md`
- **說明**：
    - 在 Git 2.23 之前，這是用來丟棄工作目錄中對單個文件的修改的主要方式。
    - 這個命令還可以用來切換分支或恢復文件到某個具體的提交（需要指定 commit hash 或分支名）。
### `git restore Readme.md`
- **用途**：將 `Readme.md` 文件恢復到最近一次提交的狀態。
- **命令**：`git restore Readme.md`
- **說明**：
    - 這個命令在 Git 2.23 之後引入，專門用來替代 `git checkout` 中與恢復文件相關的功能。
    - 它的目標是更直觀、更專門化，讓使用者更清楚指令的用途。
### 主要差異
- **歷史版本**：`git checkout` 可以用來切換分支或檢出某個具體的提交。
- **專門化**：`git restore` 更專門用於恢復工作目錄中文件的狀態，功能更加直觀。