---
Date: 2024-06-23
tags:
  - k8s
  - devops
---
>[!info]
>[https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)
## Taints
- Node affinity : 使得pod有選擇node的能力。
- Taints : 正好相反－它允許Node特定的Pod。
## Tolerations
Tolerations 應用在 Pod 上的屬性，用來表示這些 Pod 可以被調度到帶有相應污點（Taints）的節點上。容忍 ( Tolerations ) 允許調度器忽略節點上的污點，並將符合條件的 Pod 調度到這些節點。

## 演示
給Node添加一個taint, 一個taint包含一個key和value，以及效果。 比如下面這個意味者没有任何一个pod能夠schedule到這個node上，除非這個pod有一個matching toleration.
```shell
$ kubectl taint nodes k8s-worker1 key1=value1:NoSchedule   # to add
$ kubectl taint nodes k8s-worker1 key1=value1:NoSchedule-  # to delete
```

添加完之後，我們可以 `describe` 看看
![[K8s - Taints and Tolerations 1.png]]
>[!info]
>`Traints` : 這邊確實會新增一個 key1=value1:NoSchedule

建立 replica=3 的部署（k8s-worker1上不會調度任何pod）
```shell
kubectl taint nodes k8s-worker1 color=red:NoSchedule
kubectl create deployment web --image=nginx --replicas=3
```
![[K8s - Taints and Tolerations 2.png]]
>[!info]
>`kubectl taint nodes k8s-worker1 color=red:NoSchedule`: 為節點 `k8s-worker1` 添加一個污點（Taint）
>- `color=red`：這是污點的鍵和值組合，表示這個污點的標籤。
>- `NoSchedule`：這是污點的效果，表示不允許沒有相應容忍（Tolerations）的 Pod 調度到這個節點上。
>也因為這樣在創建時，才沒有在 k8s-worker1 創建 pods。

add Tolerations
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - image: nginx
        name: nginx
      tolerations:
      - key: "color"
        operator: "Equal"
        value: "red"
        effect: "NoSchedule"
```
![[K8s - Taints and Tolerations 3.png]]
>[!info]
>我們可以看到這 8 個 pod 平均的在這 worker 上面，有就是說 tolerations 剛好就 match 到我們的 worker1 上面的 Taints。

然後為什麼它不會部署到 master 原因就是因為 master 它有這個 taints ，所以如果想要使它部署在 master 節點上面的話，就要在 pod 定義 tolerations 。
### 清理資源
```shell
kubectl delete deployments.apps web
kubectl taint nodes k8s-worker1 color=red:NoSchedule-
```
## 查看原始部署在 master 節點
查看全部 pods
```shell
kubectl get pods -A -o wide
```
![[K8s - Taints and Tolerations 4.png]]

查看 flannel 運行在 master 節點資訊
```shell
kubectl describe pod kube-flannel-ds-lgfh4 --namespace kube-flannel
```

我們拉到最底下的資訊，會發現他這邊是有定義 Tolerations 的，`:NoSchedule`
![[K8s - Taints and Tolerations 5.png]]