---
Date: 2024-06-23
tags:
  - devops
  - k8s
---
## Cordoning
Cordoning 就是把一個節點標記為unschedullabel，一旦標記後，就不會再有新的pod被部署到這個節點上了。
```shell
$ kubectl cordon <node_name>
```

當我們要維護一個節點的時候，一般會透過 Cordoning 標記這個節點。
### 演示
我們創建一個 nginx pod ，且 replicas = 2 會平均創建在 worker 上，但如果給他一個 cordon 標記的話，它之後會不在該 node 上創建 pod。
![[K8s - Cordoning 1.png]]

我們改為 replicas = 4
```shell
kubectl scale deployment web --replicas 4
```
![[K8s - Cordoning 2.png]]
## drain
drain 可以 gracefully 的停止一個節點上的 Pod，並重新佈署在另一個節點上。
```shell
$ kubectl drain <node name> --ignore-daemonsets
```

如果你直接輸入 `kubectl drain <node-name>` 話，會出現 error的，原因是它無法刪除 darmonsets 這個 pod ，所以須加上 `ignore`
![[K8s - Cordoning 3.png]]

成功並轉移至 worker2 上
![[K8s - Cordoning 4.png]]
## uncordon
重新將一個節點標記為schedulable
```shell
kubectl uncordon <node_name>
```