---
Date: 2024-07-02
tags:
  - git
  - devops
---
基本命令
```shell
git log
```

限制顯示的提交數量
```shell
git log -n 5
```

單行顯示
```shell
git log --oneline
```

顯示提交的差異
```shell
git log -p
```

顯示特定作者的提交
```shell
git log --author="作者名字"
```

顯示提交的簡略統計信息
```shell
git log --stat
```

顯示提交歷史的圖形視圖
```shell
git log --graph
```

顯示特定日期範圍內的提交
```shell
git log --since="2023-01-01" --until="2023-12-31"
```

顯示特定文件的提交歷史
```shell
git log -- <文件名>
```