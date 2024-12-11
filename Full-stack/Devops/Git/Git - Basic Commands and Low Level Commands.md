---
Date: 2024-07-02
tags:
  - git
  - devops
---
## Basic Commands
- git init : 初始化一個空的 Git 儲存庫。
- git status : 顯示工作樹的狀態。
- git add : 將檔案內容新增到索引中。
- git rm : 從工作樹中移除檔案並從索引中刪除。
- git restore : 恢復工作樹中的檔案。
- git commit : 記錄對儲存庫的變更。
- git log : 顯示提交日誌。
## Low Level Commands
- git cat-file SHA1 : 顯示 Git 物件的內容和資訊
	- - `-t` (顯示 Git 物件類型)
	- `-s` (顯示 Git 物件大小)
	- `-p` (顯示 Git 物件內容)
- git ls-files : 列出儲存庫中暫存區（index）中的檔案
	- `-s` (在輸出中顯示已暫存內容的模式位、物件名稱和階段編號)