---
Date: 2024-07-20
tags:
  - git
  - devops
---
在軟體開發過程中，管理釋出的版本是至關重要的。Git 提供了 Release 分支和 Tag 的功能來幫助開發者有效地管理和標記釋出的版本。
#### 使用 Release 分支

Release 分支的主要目的是準備一個新的軟體釋出版本。這個分支允許你在即將釋出的代碼上進行最後的修改和測試，而不會影響到開發中的功能分支或主分支。
##### 1. 創建 Release 分支
從 `develop` 分支創建一個 Release 分支。Release 分支通常使用 `release/` 前綴加上版本號作為名稱，例如 `release/1.0.0`。

```shell
git checkout develop
git checkout -b release/1.0.0
```
##### 2. 在 Release 分支上進行修改
在 Release 分支上進行必要的修改，例如修復 Bug、更新文檔、調整配置等。這些修改應該是為了準備釋出版本，而不是開發新功能。
##### 3. 測試和確認
在 Release 分支上進行全面的測試，確保即將釋出的版本是穩定和可用的。
##### 4. 合併和 Tag
當 Release 分支準備好釋出時，將它合併到 `main` 分支，然後從 `main` 分支創建一個 Tag。

```shell
git checkout main
git merge release/1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"
```

接著，將 Release 分支的更動合併回 `develop` 分支，以確保開發分支也包含這些修改。

```shell
git checkout develop
git merge release/1.0.0
```

最後，刪除 Release 分支：

```shell
git branch -d release/1.0.0
```
#### 使用 Tag 標記釋出版本
Tag 是 Git 中用來標記特定提交的一種方式，通常用於標記釋出的版本。Tag 分為兩種：輕量級 Tag 和註釋 Tag。
##### 1. 輕量級 Tag
輕量級 Tag 是指向特定提交的簡單標籤，沒有附加的註釋或元數據。

```shell
git tag v1.0.0
```
##### 2. 註釋 Tag
註釋 Tag 包含更多的元數據，例如標籤名稱、電子郵件、日期和註釋等，這使得註釋 Tag 更加靈活和可追蹤。

```shell
git tag -a v1.0.0 -m "Release version 1.0.0"
```
##### 3. 推送 Tag 到遠端倉庫
創建 Tag 後，需要將它推送到遠端倉庫，以便其他開發者也能看到這些標籤。

```shell
git push origin v1.0.0
```
##### 4. 查看 Tag
可以使用以下命令來查看倉庫中的所有 Tag：

```shell
git tag
```

或者查看特定 Tag 的詳細信息：

```shell
git show v1.0.0
```
#### Release 分支和 Tag 的最佳實踐
1. **遵循語義化版本號**：使用語義化版本號 (SemVer) 來標記釋出版本，例如 `v1.0.0`。這有助於清晰地了解版本間的變更。
2. **使用註釋 Tag**：儘可能使用註釋 Tag，這樣可以附加更多有用的信息，方便未來的查閱和追蹤。
3. **定期清理分支**：釋出後，刪除已完成的 Release 分支，以保持倉庫的整潔。
4. **標記穩定版本**：在 `main` 分支上標記每個穩定釋出的版本，確保有一個可回溯的版本歷史。
5. **釋出說明**：每次釋出時，撰寫詳細的釋出說明 (Release Notes)，包括新增功能、修復的 Bug 和已知問題等。