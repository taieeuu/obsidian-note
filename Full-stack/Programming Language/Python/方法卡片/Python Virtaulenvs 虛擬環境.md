---
Date: 2024-10-13
relation:
  - "[[Python Pyenv 版本的工具]]"
  - "[[Python Virtaulenvs 虛擬環境]]"
---
## 安裝 virtualenv
```shell
pip install virtualenv
```
## 創建虛擬環境
在你專案的目錄中，使用 `virtualenv` 創建一個虛擬環境。例如，要在 `my_project` 目錄中創建一個名為 `venv` 的虛擬環境：

```shell
virtualenv ~/.virtualenvs/project_name
```

你也可以指定 Python 版本來創建虛擬環境。例如，使用 Python 3.9 創建虛擬環境：

```shell
virtualenv -p python3.9 venv
```
## 停止虛擬環境
```shell
deactivate
```
## 刪除虛擬環境
```shell
rm -rf venv
```
