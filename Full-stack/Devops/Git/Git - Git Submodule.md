---
Date: 2024-07-18
tags:
  - devops
  - git
---
Git Submodule 是 Git 提供的一個功能，用來在一個 Git 倉庫中嵌套另一個 Git 倉庫。這對於需要在一個大型項目中包含其他獨立項目或庫的情況特別有用。使用 Git Submodule，可以將外部的 Git 倉庫作為子倉庫包含在你的主倉庫中，同時保持它們的獨立性。
### 主要功能和優點
1. **嵌套倉庫**：允許在一個倉庫中包含其他獨立的 Git 倉庫，保持它們的版本控制。
2. **版本管理**：每個子模組都有自己的提交歷史和分支，並且可以固定在某個特定的提交上。
3. **獨立開發**：子模組的變更不會直接影響主倉庫，反之亦然，這使得模組化開發更加方便。
#### 添加子模組
使用 `git submodule add` 命令來添加子模組。
```shell
git submodule add https://github.com/example/repo path/to/submodule
```
這會將指定的 Git 倉庫克隆到 `path/to/submodule` 目錄中，並在根目錄下的 `.gitmodules` 文件中添加相關信息。`.gitmodules` 文件記錄了子模組的路徑和 URL。
```shell
[submodule "path/to/submodule"] path = path/to/submodule url = https://github.com/example/repo
```

這個過程會在 `.git` 目錄下創建一個名為 `modules` 的子目錄，其中包含子模組的相關信息。每個子模組會有自己的 Git 環境，就像在主倉庫的 `.git` 目錄中一樣。

子模組的目錄（如 `path/to/submodule`）實際上是指向特定的提交（commit），而不是持續跟蹤子倉庫的最新狀態。當你將包含子模組的變更推送到遠程倉庫時，子模組的引用（commit ID）會被記錄下來，而不是其最新的狀態。
#### 初始化和更新子模組
在克隆包含子模組的倉庫後，你需要初始化並更新子模組：
```shell
git submodule init
git submodule update --recursive
```
`git submodule init` 會根據 `.gitmodules` 文件中的配置信息初始化子模組，而 `git submodule update` 則會檢出子模組的最新提交。
#### 3. 拉取包含子模組的倉庫
當你拉取一個包含子模組的倉庫時，需要更新子模組：
```shell
git pull
git submodule update --init --recursive
```
這樣可以確保所有子模組都被正確初始化並更新到指定的提交。
#### 拉取最新 submodule 
```shell
git pull
git submodule update --remote
```

