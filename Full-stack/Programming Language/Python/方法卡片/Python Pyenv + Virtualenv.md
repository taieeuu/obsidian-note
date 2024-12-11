---
Date: 2024-10-17
relation:
  - "[[Python Pyenv 版本的工具]]"
  - "[[Python Virtaulenvs 虛擬環境]]"
---
## 摘要
看標題就可以知道，他是一個將[[Python Virtaulenvs 虛擬環境]]與[[Python Pyenv 版本的工具]]，整合起來的工具，他與 Conda 使用方法很類似，應該說幾乎沒有任何的不同（除了關於其他語言），目前體驗下來。
## 安裝
```shell
brew install pyenv-virtualenv
```

安裝後，將其加入到你的 shell 環境中：

```shell
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.zshrc
source ~/.zshrc
```
## 使用 `pyenv` 創建虛擬環境
你可以透過 `pyenv virtualenv` 命令直接創建一個虛擬環境並同時指定 Python 版本。

```shell
pyenv virtualenv 3.10.0 myenv
```

這會使用 Python 3.10.0 創建一個名為 `myenv` 的虛擬環境。
## 切換虛擬環境
創建完虛擬環境後，你可以使用 `pyenv activate` 來啟動該虛擬環境：

```shell
pyenv activate myenv
```

退出虛擬環境：

```shell
pyenv deactivate
```
## 設定專案使用虛擬環境
如果你希望某個專案在該目錄下自動使用某個虛擬環境，你可以使用：

```shell
pyenv local myenv
```
## 查看虛擬環境列表
```shell
pyenv virtualenvs
```
## 刪除虛擬環境
```shell
pyenv uninstall myenv
```