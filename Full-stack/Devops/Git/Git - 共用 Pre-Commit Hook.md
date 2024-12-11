---
Date: 2024-07-18
tags:
  - devops
  - git
---
我們可以使用 `python` 提供的 `pre-commit package` 來生成使用 `python script` 寫的 `git pre-commit hook`。

### 安裝
```shell
pip install pre-commit
```

### 生成 scripts 命令
```shell
pre-commit install
```

### 配置 `.pre-commit-config.yaml`
在你的 Git 倉庫的根目錄創建一個 `.pre-commit-config.yaml` 文件，並在其中配置你想要使用的 hooks。例如：
```shell
repos:
  - repo: https://github.com/ambv/black
    rev: stable
    hooks:
      - id: black
        language_version: python3.7
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v1.2.3
    hooks:
      - id: flake8
```
### 安裝 hooks
運行以下命令來安裝並啟用 `pre-commit` hooks：
```shell
pre-commit install
```
### 使用 `pre-commit`
現在，每次你執行 `git commit` 時，`pre-commit` 都會根據配置文件運行相應的 hooks。這些 hooks 會檢查和格式化你的代碼，如果發現問題，提交會被阻止，並顯示錯誤訊息。
### 總結
- **`pre-commit` 是用 Python 編寫的工具**：它是一個可以通過 pip 安裝的 Python 包。
- **`pre-commit` 支持多種語言和工具**：儘管它是用 Python 編寫的，但它可以管理和運行多種語言的 hooks。
- **易於配置和使用**：通過簡單的 YAML 配置文件和命令行工具，你可以輕鬆設置和管理 Git hooks。