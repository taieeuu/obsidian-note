---
Date: 2024-10-17
relation:
  - "[[Python Pyenv 版本的工具]]"
  - "[[Python Virtaulenvs 虛擬環境]]"
---
## 安裝
`pyenv` 是管理 Python 版本的工具，而不是一個 Python 套件，所以無法用 `pip` 安裝。

```shell
brew install pyenv
```

安裝後，將 `pyenv` 添加到 shell 環境：

```shell
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init --path)"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
source ~/.zshrc
```
## 列出可安裝的 Python 版本
查看所有可以安裝的 Python 版本：

```shell
pyenv install --list
```
## 安裝指定的 Python 版本
```shell
pyenv install 3.10.0
```
## 設定全局 Python 版本
你可以設置一個全局（默認）使用的 Python 版本：

```shell
pyenv global 3.10.0
```
## 設定專案的 Python 版本
在特定的專案中使用不同版本的 Python，可以進入該專案資料夾並執行：

```shell
pyenv local 3.8.0
```

這會在專案資料夾中創建一個 `.python-version` 檔案，紀錄專案使用的 Python 版本。
## 切換 Python 版本
可以使用 `pyenv versions` 列出已安裝的 Python 版本，並隨時切換：

```shell
pyenv versions
```

例如，切換到 Python 3.9.0：

```shell
# 全域
pyenv global 3.9.0

＃非全域
pyenv install 3.10
```

