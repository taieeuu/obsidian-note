---
Date: 2024-07-20
tags:
  - devops
  - git
---
## 什麼是 `.gitignore`？
`.gitignore` 是一個文本文件，用來告訴 Git 哪些文件或目錄應該被忽略，不需要被版本控制。這對於那些不需要提交到倉庫的文件（例如編譯生成的文件、臨時文件、個人設定文件等）非常有用。

## 基本語法
- 每行一個模式。
- 以井號（#）開頭的行為註釋行。
- 可以使用 `!` 來否定某個模式，表示不忽略該文件或目錄。
- 支援使用通配符 `*` 來匹配多個字符，`?` 匹配單個字符。
- 使用斜線 `/` 表示目錄結構。

## 全域的`.gitignore`
除了專案根目錄下的 `.gitignore` 文件，Git 還支援設置全域性的 `.gitignore`，這對於那些在所有專案中都應該被忽略的文件非常有用。
#### 全域性 `.gitignore` 文件
1、**建立全域性 `.gitignore` 文件：**

首先，建立一個全域性的 `.gitignore` 文件，例如在你的主目錄下：

```shell
touch ~/.gitignore_global
```

2、**配置 Git 使用全域性 `.gitignore` 文件：**

然後，告訴 Git 使用這個全域性的 `.gitignore` 文件

```shell
git config --global core.excludesfile ~/.gitignore_global
```

3、**編輯全域性 `.gitignore` 文件：**

現在，你可以編輯這個文件來添加你想要全域性忽略的文件或目錄。例如：

```shell
# OS generated files #
######################
.DS_Store
Thumbs.db

# Log files #
##############
*.log

# Node modules #
################
node_modules/

# Compiled source #
###################
*.com
*.class
*.dll
*.exe
*.o
*.so

# Python #
##########
*.py[cod]
__pycache__/
*.so
```
## 本地端專案的 `.gitignore`
除了專案根目錄的 `.gitignore` 文件和全域性的 `.gitignore` 設定外，Git 還提供了一個本地專案的忽略文件，位於 `.git/info/exclude`。這個文件僅對當前倉庫有效，且不會被提交到遠端倉庫，非常適合用來忽略一些本地開發過程中生成的文件。
#### `.git/info/exclude` 特性
- **本地性**：只對當前倉庫有效，不會影響其他倉庫。
- **不提交**：不會被提交到遠端倉庫，因此不會影響其他開發者。
#### 使用方法
1、**打開 `exclude` 文件**：
    
進入你的 Git 專案目錄，然後打開 `.git/info/exclude` 文件：

```shell
nano .git/info/exclude
```

2、**編輯 `exclude` 文件**：

在文件中添加你想要忽略的文件或目錄的模式。與 `.gitignore` 文件使用相同的語法。例如：

```shell
# 忽略所有的 .env 文件
*.env

# 忽略編譯生成的文件
*.o
*.so
*.a

# 忽略 IDE 設定文件
.vscode/
*.code-workspace
```

#### 其他注意事項
- **優先級**：`.git/info/exclude` 的優先級高於全域性的 `.gitignore` 文件，但低於專案根目錄下的 `.gitignore` 文件。
- **用途**：`exclude` 文件適用於那些不需要被提交到版本控制中，且僅在本地開發環境中生成的臨時文件或設定文件。
#### 當前倉庫的忽略文件
使用 `.git/info/exclude` 可以有效地管理和忽略本地開發過程中產生的特定文件，而不需要修改或影響全域性或專案級別的 `.gitignore` 文件。這對於確保倉庫的整潔和避免不必要的文件提交非常有幫助。