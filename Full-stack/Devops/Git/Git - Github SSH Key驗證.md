---
Date: 2024-07-15
tags:
  - devops
  - git
---
生成好 `id_rsa` 之後到 Github 中配置 SSH Key ，之後的 remote 要使用 `ssh` 模式才會啟動 SSH 驗證方式。
![[Pasted image 20240715214711.png]]

由於這邊我是多帳號 Git ，詳細可參考 [[GIT 多帳號登入]]
修改`.git/config`中的`remote[orgin]`

原始
```shell
[remote "origin"]
	url = git@github.com:<user>/git-test.git
	fetch = +refs/heads/*:refs/remotes/origin/*
```

修改後   **注意**`github-<user>`這邊要跟你設定的 Host 一致。
```shell
[remote "origin"]
	url = git@github-<user>:<user>/git-test.git
	fetch = +refs/heads/*:refs/remotes/origin/*
```