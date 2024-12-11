---
Date: 2024-06-04
tags:
  - docker
  - devops
---
## Overly 網路

![[無標題-2023-12-22-2358.png]]
![[Pasted image 20240606221512.png]]
>[!info]
>每個容器都有兩個網卡 eth0、eth1 ，

![[Pasted image 20240606222407.png]]
>[!info]
>可以看到輸入`ip a`確實有兩張網卡，且每次對外會透過`路油表`來進行通訊，默認路由走`172.18.0.1`eth1 這個接口出去，如果`10.0.1.0/24`會走 eht0 。
#### 測試
我可以使用一台機器執行，下面指令抓取封包
```shell
sudo tcpdump -i <接口名稱> port 4789
```
>[!info]
>- 4789: vxlan的接口

另一台則執行 ping 的指令
```shell
ping <ip> -c 4
```
>[!info]
>ping ip 4次

然後我們就會發現第一台機器有了8個封包，那位甚麼事8個呢，原因是ping是一來一回的，所以有8個。