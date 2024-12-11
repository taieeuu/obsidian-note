---
Date: 2024-06-23
tags:
  - k8s
  - devops
---
所謂manual Scheduling人工調度，就是直接指定pod運行的節點。

可使用 `nodeName` 來進行指定
```yml
apiVersion: v1
kind: Pod
metadata:
  name: web
spec:
   nodeName: 'k8s-worker1'
   containers:
    - name: nginx-container
      image: nginx:latest
```
兩個問題：
- 1. taint的節點是否可以接受這個pod？答案是 yes
- 2. cordon的節點是否可以接受這個pod？答案是 yes

**補充**
![[K8s - Manual Scheduling 1.png]]
>[!info]
>該錯誤表示無法排空節點，因為 `default/web` Pod 沒有控制器管理（沒有 Deployment 或 ReplicaSet 等），你需要使用 `--force` 來強制刪除這些 Pod。
>在其他節點上沒有這些 pod ，一旦刪除則就刪除了。

