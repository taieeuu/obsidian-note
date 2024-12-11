---
Date: 2024-04-22
tags:
  - devops
  - git
---
# 前言
同一台PC使用兩隻Git帳號會有存取的權限問題，解決方法可以使用ssh-key來解決。
# Windows
## 前置
- 查看windows中的ssh-agent是否有開
```shell
Get-Service -Name ssh-agent
```
- 設置ssh-agent服務啟動類行為 "手動(Manual)"，意味著服務部會在系統啟動自動運行，需要手動啟用。
```shell
Set-Service ssh-agent -StartupType Manual
```
- 啟動ssh-agent
```shell
Start-Service -Name ssh-agent
```
## 生成金鑰
```shell
ssh-keygen -t rsa -C "example@email.com"
```

> [!info]
>Enter file in which to save the key : 設置自己的路徑以及金鑰名稱
>再Enter
## 返回Github設置SSH-KEY
## 如有多個帳號，重複上述步驟
## 添加ssh key 至 ssh 代理
## 添加.ssh/config
```md
# Defult
Host github-dtyty1895
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_dtyty1895

# Other
Host github-taieeuu
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_taieeuu
```
## 推送數據
```shell
echo "# test3" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:taieeuu/test3.git
git push -u origin main
```
**注意**
![[GIT 多帳號登入.png]]
這邊的github.com，需要改成你在~/.ssh/config中，設置的Host，以上述來說，改為github-dtyty1895