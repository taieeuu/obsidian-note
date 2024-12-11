---
Date: 2024-06-23
tags:
  - k8s
  - devops
---
## Pod Scheduling
Scheduling是找到一個適合的節點去運行pod的過程，而這個尋找主要是透過`kube-scheduler`實現的。

以創建Pod為例
- 透過api伺服器取得節點信息。
- 節點選擇： - 過濾（各種過濾過濾，硬體的限制） - 評分（過濾出的節點，進一步評分篩選） - 綁定（選擇出一個節點，最後和API物件綁定）。
- 更新pod的信息，包括在哪個node上。
- 被選節點的kubelet透過監控api-server得知自己被選擇創造一個pod。
- kubelet驅動容器運行時建立容器並啟動。

我們監聽後，創建 nginx pods
![[k8s - Scheduling 1.png]]
>[!info]
>我們會發現它自動 scheduling 到 worker2 上面，那其實我們也可以自己設定 pod 的 scheduling。
## Node Selector
`nodeSelector`使用標籤和選擇器將 Pod 指派給節點
- 給節點設定label 為節點套用標籤
- 調度程式會將 Pod 指派給具有匹配標籤的節點

簡單來說就是 key-value 的匹配
### Pod with nodeSelector
nginx.yml 範例
```yml
apiVersion: v1
kind: Pod
metadata:
  name: web
spec:
  containers:
  - name: hello-world
    image: nginx
  nodeSelector:
    hardware: local_gpu
```

apply
```shell
kubectl apply -f nginx.yml
```
![[k8s - Scheduling 2.png]]
>[!info]
>我們會發現 Status 他會是 Pending 狀態，這是因為 node 中沒有一個 label是 hardware=local_gpu。

我們看看 web 的 詳細資訊中的 events
```shell
kubectl describe pod web 
```
![[k8s - Scheduling 3.png]]
>[!info]
>- 其中一個是 master 節點不滿足
>- 三個節點沒有 match 到 selector


我們在 node 上，加上 label 看看
![[k8s - Scheduling 4.png]]
![[k8s - Scheduling 5.png]]
>[!info]
>加上 label 後，確實可以匹配成功，`describe pod web` event 也是有成功匹配的事件存在。

那如果我們把 k8s-worker1 節點的 label 拔除呢
![[k8s - Scheduling 6.png]]
>[!info]
>我們發現對已經運行的 pod ，如果之後不滿足，它也是會照樣運行。

