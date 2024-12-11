---
Date: 2024-07-15
---
先在本地生成 `id_rsa`，以下命令會在 `~/.ssh` 資料夾中生成 `id_rsa` 和 `id_rsa.pub` 文件。
```shell
ssh-keygen
```

在將生成的 public key 傳至要連線的`.ssh`資料夾底下
```shell
scp ~/.ssh/id-rsa-test.pub <username>@host:<dir>
```

接下來就可以直接登入了
```shell
ssh <username>@host
```