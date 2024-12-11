---
Date: 2024-07-16
tags:
  - devops
  - git
---
Git Tag 是用來標記某一特定提交的快照（commit snapshot）。通常用於標記版本號（例如，v1.0.0）或釋出點，方便未來查找和管理。
## Git Tag 的類型
Git 提供兩種標籤類型：
1. **輕量標籤（Lightweight Tag）**：
    - 輕量標籤只是某一提交的簡單標記，不包含任何額外資訊。
    - 本質上只是某一提交的引用。
    - 指令：`git tag <tagname>`
2. **註釋標籤（Annotated Tag）**：
    - 註釋標籤則包含更多資訊，例如標籤的創建者、日期和訊息。
    - 此標籤存儲在 Git 數據庫中，可以進行簽名（sign）。
    - 指令：`git tag -a <tagname> -m "tag message"`
## 創建標籤
### 創建輕量標籤
當創建一個輕量標籤時，`.git` 目錄下會多出一個檔案在 `refs/tags` 底下。這個檔案只向某一特定的 commit 指向。
此時，在 `.git/refs/tags/` 目錄下會生成一個名為 `v1.0.0` 的檔案，該檔案的內容是對應的提交哈希值（commit hash）。
```shell
git tag v1.0.0
```
### 創建註釋標籤
註釋標籤（Annotated Tag）是 Git 中用來標記特定提交的標籤，並包含了更多的元數據資訊，例如標籤的創建者、日期、訊息，甚至可以進行簽名（sign）。

與創建輕量標籤一樣，`.git` 目錄下會多出一個檔案在 `refs/tags` 底下。這個檔案只向某一特定的 commit 指向，不同的是他會在`.git/objects` 目錄下產生一個 tag 屬性的哈希文件。
```shell
git tag -a v1.0.0 -m "Release version 1.0.0"
```
>[!info]
>**指令**：`git tag -a v1.0.0 -m "Release version 1.0.0"`
>- `-a`：表示創建一個註釋標籤（`-a` 代表 `annotated`）。
>- `v1.0.0`：是標籤的名稱。
>- `-m "Release version 1.0.0"`：添加一條標籤訊息，這條訊息會與標籤一起存儲。
### 列出標籤
列出所有標籤：
```shell
git tag
```

顯示包含特定模式的標籤：
```shell
git tag -l "v1.0.*"
```
### 刪除標籤
本地刪除標籤：
```shell
git tag -d v1.0.0
```
## 同步標籤
使用 `git fetch` 來獲取遠端倉庫的最新變化，包括新增和修改的標籤，但這不會自動刪除本地已被遠端刪除的標籤。
```shell
git fetch
```
## 推送標籤到遠端
推送單個標籤到遠端：
```shell
git push origin v1.0.0
```

遠端刪除標籤：
```shell
git push origin --delete tag v1.0.0
```

推送所有標籤到遠端：
```shell
git push origin --tags
```

**注意:** 
推送遠端 `tag` 時間
1. 輕量標籤(Lightweight Tag)
	- 這邊推送至遠端顯示的 `tag` 是根據 `commit` 何時提交。
2. 註釋標籤(Annotated Tag)
	- 推送至遠端顯示的 `tag` 時間是根據 `tag` 的創建時間，而不是 `commit` 的提交時間，因為它會創建一個新的 `object`，而輕量標籤則不會。
## 情境
如果遠端刪除了 `tags`，並不能通過 `git fetch` 來獲取這些刪除資訊並自動刪除本地的 `tags`。需要手動刪除本地的 `tags`。實際的 `tags` 對應的提交物件並不會被刪除，這些提交物件仍然存在於本地的 Git 數據庫中。必須要通過`git tag -d <version>`
