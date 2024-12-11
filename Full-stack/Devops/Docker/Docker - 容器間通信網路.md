---
Date: 2024-06-02
tags:
  - docker
  - devops
---
## 網路實際通信
有兩台電腦，我們想要讓這兩台之間能夠通信，我們需要連接一條網路線，且還要都在同一個網段下。
![[Pasted image 20240602020056.png|465]]
多個機器，我們會引入一個機器，我們稱為 交換器 來做許多的事情，像是如果192.168.1.2/24這台機器要發送數據，會先送到交換器上面， 然後通過交換器的這個轉送的規則， 然後把這台把這個封包轉送到這個這個192.168.1.5 /24上面去。 這樣他們兩個之間呢， 就可以通信了。
![[Pasted image 20240602020438.png]]

有了上述概念，我們可以進入正題 ! 
在一台電腦上可以創建許多的容器，那他們之間為甚麼可以進行通信呢 ?
接下來就要介紹 Bridge 網路。

我們從 `docker network ls`來看，會發現有三個原始的 network 以下我們來介紹一下。

```shell
$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
1847e179a316   bridge    bridge    local
a647a4ad0b4f   host      host      local
fbd81b56c009   none      null      local
```
