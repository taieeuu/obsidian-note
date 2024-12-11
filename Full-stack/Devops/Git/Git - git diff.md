---
Date: 2024-07-06
tags:
  - devops
  - git
---
這個指令在線在基本已經很少用到了，因為現在的 Git IDE 提供給我們比較強大的比較文件差別的功能。

在 VSCode 中，檔案名稱旁邊顯示的 目前 Git 的狀態。PS. 當然我們須先下載 vscode 中的 git extension。
常見的標記還有：
- **A** :  "Added"（新增）的狀態。這表示這些檔案已經被新增到 Git 暫存區，但尚未提交到版本庫。
- **M**：Modified（已修改） - 檔案已被修改，但尚未提交。
- **D**：Deleted（已刪除） - 檔案已被刪除，但尚未提交。
- **U**：Untracked（未追蹤） - 檔案未被 Git 追蹤，未加入到暫存區。
### 在 VSCode 中，查看修改了哪些資料
點擊框框中的藍色地方，我們可以查看與上一個 commit 差異在哪裡。
![[Pasted image 20240706102615.png]]

![[Pasted image 20240706102630.png]]

旁邊的 git extension 顯示的 `1` 代表目前有多少個檔案與上一個 commit 不同
![[Pasted image 20240706103014.png]]

我們可以進入 Git Extensions 中，我們可以點擊已修改的文件，系統會顯示一個左右兩邊比對的視圖，左邊顯示文件的舊版本，右邊顯示文件的新版本，讓我們清楚地比對並查看文件的具體修改內容。
![[Pasted image 20240706103955.png]]

### 在 terminal 查看修改了哪些資料
原始
![[Pasted image 20240706105803.png]]

使用 `git status` 查看狀態
```shell
G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   Readme.md

no changes added to commit (use "git add" and/or "git commit -a")
```
>[!info]
>這邊的 modified 也就是我們在 vscode 查看到檔案旁邊的 **M** 。

使用 `git diff` 查看
```shell
G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (master)
$ git diff
diff --git a/Readme.md b/Readme.md
index f3c24f9..2d9849f 100644
--- a/Readme.md
+++ b/Readme.md
@@ -4,7 +4,7 @@ git demo repository

 ## basic git command

-- git init
+- git init ()
 - git status

 ## pro git command
@@ -27,3 +27,5 @@ git demo repository
   - **用途**：建立一個新的分支並立即切換到該分支。
   - **筆記**：這個指令相當於同時執行 `git branch <branch_name>` 和 `git checkout <branch_name>`，方便快捷地開始在新分支上工作。
 - **git branch -m <old_name> <new_name>** - **用途**：重命名分支。 - **筆記**：這個指令可以用來改變當前分支或指定分支的名稱。如果 `<new_name>` 已存在，會返回錯誤訊息。
+
+Thanks!

G713QM@LAPTOP-OKJPH9JJ MINGW64 /d/Project/Note/Git/git-demo (master)
$
```
>[!info]
>1. **檔案狀態變更**：
>	- 指令顯示了 `Readme.md` 文件的差異，並標記了文件從舊版本 (`f3c24f9`) 到新版本 (`2d9849f`) 的變更。`2d9849f`為目前版本的 `SHA1` 值，所以他是還沒進行任何commit 的，所以無法使用 `git cat-file <sha1-name>` 查看到他的內容的。
>2. **行級別變更**：
>	- `--- a/Readme.md` 和 `+++ b/Readme.md` 顯示了文件的舊版本和新版本。
>	- `@@ -4,7 +4,7 @@` 和 `@@ -27,3 +27,5 @@` 表示變更的行數範圍。
>3. **具體變更內容**：
>	- 行 `- git init` 被修改為 `+ git init ()`，表示在 `git init` 後新增了括號。
>	- 最後添加了兩行空行和 `Thanks!`。

