---
Date: 2024-06-02
tags:
  - docker
  - devops
---
## 網路命名空間
Linux的Namespace（命名空間）技術是一種隔離技術，常用的Namespace有user namespace, process namespace, network namespace等

在Docker容器中，不同的容器透過Network namespace進行了隔離，也就是不同的容器有各自的IP位址，路由表等，互不影響。

詳細實作可參考 https://www.karlrupp.net/en/computer/nat_tutorial