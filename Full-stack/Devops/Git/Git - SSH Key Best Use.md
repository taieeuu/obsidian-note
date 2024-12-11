---
Date: 2024-07-15
tags:
  - git
  - devops
---
當使用 SSH key 來進行遠程伺服器管理時，遵循最佳實踐可以顯著提高安全性。

通常在工作中，我們會使用公司提供的 Git 帳號來管理和訪問代碼庫。而我們自己也可能有私人 Git 帳號，或同時使用 GitHub 與 GitLab。在這種情況下，使用 SSH key 來進行管理是非常必要的。

我們可以藉由生成 key 時，藉此命名名稱
```shell
D:\Project\Note\git-test>ssh-keygen -C "<user>@gmail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\G713QM/.ssh/id_rsa):
```

那這時其實還是無法使用 SSH 驗證的，因為 Github 驗證是使用 id_rsa 的這個預設去抓取 private key，所以這時我們必須設置 `.ssh` 來告訴，對應的 key 是哪把。
```shell
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_github

Host gitlab.com
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/id_rsa_gitlab
```