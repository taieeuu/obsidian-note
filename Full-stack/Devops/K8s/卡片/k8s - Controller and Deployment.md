---
Date: 2024-06-20
tags:
  - k8s
  - devops
---
## Controller manager and controller
A controller 追蹤至少一種 Kubernetes 資源類型。這些物件具有一個 spec 欄位，`表示desired state`。該資源的控制器負責使當前狀態更接近於該期望的狀態。

- kube controller Manager
- cloud controller Manager

### Controllers
控制器負責管理和維護 Kubernetes 資源的期望狀態。
#### Pod 控制器
- **ReplicaSet**: 確保運行指定數量的 pod 副本。
- **Deployment**: 管理 ReplicaSets 並提供聲明式更新。
- **DaemonSet**: 確保所有（或部分）節點運行一個 pod 副本。
- **StatefulSet**: 管理有狀態的應用，提供穩定的身份和存儲。
- **Job**: 創建一個或多個 pod 並確保它們成功完成。
- **CronJob**: 根據時間計劃管理任務。
#### Other Controllers
- **Node**: 管理節點。
- **Service**: 管理服務。
- **Endpoint**: 管理服務的端點。

### System Pods
```shell
tai@tai-virtual-machine:~$ kubectl get all -A
```
![[k8s - Controller and Deployment 1.png]]
>[!info]
>可以看到我們的 name 有 service、daemonset、deployment、replicaset，這些就是我們上述所提到的 Pod Controller。
>- **daemonset.apps/kube-flannel-ds**: 主要作用是確保每個節點都運行這一個特定的 pod，常用於部署網絡插件（如 Flannel）或系統監控代理等服務。我們這個是群集是三個節點，所以這邊顯示 3。
>- **`daemonset.apps/kube-flannel-ds**` 是一個 DaemonSet，用於部署 Flannel 網絡插件，確保 Kubernetes 集群中的每個節點上都運行 Flannel pod。
>- **`deployment.apps/coredns`** 是一個 Kubernetes 部署，用於管理 CoreDNS 服務。CoreDNS 是 Kubernetes 的 DNS 服務器，負責解析集群內部服務的 DNS 查詢。

## 標籤 (Labels) and 標籤選擇器 ( Labels Selector )
- 標籤( Labels )是一種鍵值對，用來為 Kubernetes 資源（如 Pod、節點等）打上標記。這些標籤可以幫助你組織和篩選資源。每個資源可以有多個標籤，每個標籤都由一個鍵和一個值組成。
- 標籤選擇器( Labels Selector )用於選擇或查詢帶有特定標籤的資源。它們在很多情況下使用，如服務選擇器、配額、配置和部署等。

#### 通過 YAML 文件（Declaratively）
在 YAML 文件中聲明標籤，可以在資源的 `metadata` 部分添加 `labels` 字段。例如，以下是三個 Pod 的定義，
```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-1
  labels:
    app: v1
    tier: PROD
spec:
  containers:
  - name: nginx
    image: nginx
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-2
  labels:
    app: v1
    tier: ACC
spec:
  containers:
  - name: nginx
    image: nginx
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-3
  labels:
    app: v1
    tier: TEST
spec:
  containers:
  - name: nginx
    image: nginx
```
>[!info]
>這三個 Pod 都屬於同一個應用 (`app: v1`)，但它們分別位於不同的環境（PROD 生產、ACC 驗收和 TEST 測試）。這種標籤設置有助於組織和管理不同環境中的 Pod，使得資源管理更加簡單和高效。

```shell
tai@tai-virtual-machine:~$ kubectl apply -f label-pod.yml
pod/nginx-pod-1 created
pod/nginx-pod-2 created
pod/nginx-pod-3 created
tai@tai-virtual-machine:~$ kubectl get pods
NAME          READY   STATUS              RESTARTS   AGE
nginx-pod-1   1/1     Running             0          5s
nginx-pod-2   1/1     Running             0          5s
nginx-pod-3   0/1     ContainerCreating   0          5s
tai@tai-virtual-machine:~$ kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
nginx-pod-1   1/1     Running   0          7s
nginx-pod-2   1/1     Running   0          7s
nginx-pod-3   1/1     Running   0          7s
tai@tai-virtual-machine:~$ kubectl get pods --show-labels
NAME          READY   STATUS    RESTARTS   AGE   LABELS
nginx-pod-1   1/1     Running   0          27s   app=v1,tier=PROD
nginx-pod-2   1/1     Running   0          27s   app=v1,tier=ACC
nginx-pod-3   1/1     Running   0          27s   app=v1,tier=TEST
```
>[!info]
>可以看到我們確實已經創建了三個 pods 並且副有 labels。
#### 通過 `kubectl` 命令行工具 
創建標籤
```shell
tai@tai-virtual-machine:~$ kubectl label pod nginx-pod-1 color=red
pod/nginx-pod-1 labeled
tai@tai-virtual-machine:~$ kubectl get pods --show-labels
NAME          READY   STATUS    RESTARTS   AGE    LABELS
nginx-pod-1   1/1     Running   0          121m   app=v1,color=red,tier=PROD
nginx-pod-2   1/1     Running   0          121m   app=v1,tier=ACC
nginx-pod-3   1/1     Running   0          121m   app=v1,tier=TEST
tai@tai-virtual-machine:~$
```

複寫標籤
```shell
tai@tai-virtual-machine:~$ kubectl label pod nginx-pod-1 color=blue --overwrite
pod/nginx-pod-1 labeled
tai@tai-virtual-machine:~$ kubectl get pods --show-labels
NAME          READY   STATUS    RESTARTS   AGE    LABELS
nginx-pod-1   1/1     Running   0          122m   app=v1,color=blue,tier=PROD
nginx-pod-2   1/1     Running   0          122m   app=v1,tier=ACC
nginx-pod-3   1/1     Running   0          122m   app=v1,tier=TEST
```
>[!info]
>如果不加上`--overwrite`的話，會出現錯誤訊息，顯示標籤已存在，須加上`--overwrite`。

刪除標籤
```shell
tai@tai-virtual-machine:~$ kubectl label pod nginx-pod-1 color-
pod/nginx-pod-1 unlabeled
tai@tai-virtual-machine:~$ kubectl get pods --show-labels
NAME          READY   STATUS    RESTARTS   AGE    LABELS
nginx-pod-1   1/1     Running   0          131m   app=v1,tier=PROD
nginx-pod-2   1/1     Running   0          131m   app=v1,tier=ACC
nginx-pod-3   1/1     Running   0          131m   app=v1,tier=TEST
```

過濾標籤
```shell
tai@tai-virtual-machine:~$ kubectl get pods --selector tier=PROD
NAME          READY   STATUS    RESTARTS   AGE
nginx-pod-1   1/1     Running   0          134m
tai@tai-virtual-machine:~$ kubectl get pods --selector tier=PROD --show-labels
NAME          READY   STATUS    RESTARTS   AGE    LABELS
nginx-pod-1   1/1     Running   0          135m   app=v1,tier=PROD
```

過濾多個標籤
```shell
tai@tai-virtual-machine:~$ kubectl get pods --show-labels -l "tier in (PROD, ACC)"
NAME          READY   STATUS    RESTARTS   AGE    LABELS
nginx-pod-1   1/1     Running   0          136m   app=v1,tier=PROD
nginx-pod-2   1/1     Running   0          136m   app=v1,tier=ACC
tai@tai-virtual-machine:~$ kubectl get pods --show-labels -l "tier notin (PROD, ACC)"
NAME          READY   STATUS    RESTARTS   AGE    LABELS
nginx-pod-3   1/1     Running   0          137m   app=v1,tier=TEST
```
### How k8s uses labels ? 
- Controllers 和 Services 使用選擇器匹配 Pod
- Pod調度，調度到特定Node
## Deployment Controller And ReplicaSet Controller
在 Deployment 中描述期望狀態，Deployment Controller 會以受控的速率將實際狀態更改為期望狀態。可以定義 Deployment 來創建新的 ReplicaSet，或者移除現有的 Deployment 並使用新的 Deployment 接管它們的所有資源。

### 查看 Deployment 與 ReplicaSet 關係
查看所有的資源
```shell
kubectl get all -A
```

在查看 `kube-system` 命名空間中的所有 Deployment controller
```shell
kubectl get deployments.apps --namespace kube-system
```
![[k8s - Controller and Deployment 2.png]]
>[!info]
>可以看到我們先獲取的整體的資源，再接著過濾 namespace=kube-system 中是 deployments controller 的資訊。

切換 `namespace` 至 `kube-system`
```shell
tai@tai-virtual-machine:~$ kubectl config set-context --current --namespace kube-system
```

查看 coredns 詳情
```shell
tai@tai-virtual-machine:~$ kubectl get deployments.apps
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
coredns   2/2     2            2           7d
tai@tai-virtual-machine:~$ kubectl describe deployments.apps coredns
```
![[k8s - Controller and Deployment 3.png]]
>[!info]
>- **Pod Template 中的 Labels**:
>	- 用於標識由這個模板創建的 Pod。
>	- 每個由這個模板創建的 Pod 都會包含這些標籤。
>- **Selector 中的 Labels**:  
>	- 用於選擇這個 Deployment 管理的 Pod。
>	- 確保只有包含這些標籤的 Pod 才會被這個 Deployment 管理。
>- **Deployment 自身的 Labels**:
>	- 用於標識這個 Deployment 資源。 
>	- 通常用於組織和查找 Deployment 資源。

然後我們可以看到最後面有一個 `NewReplicaSet` ，它顯示了**與這個 Deployment 關聯的新 ReplicaSet 的名稱和狀態。**

![[k8s - Controller and Deployment 4.png]]
>[!info]
>可以看到 Controlled By ，意思也就是說 ReplicaSet 是被我們 Deployment/coredns 所控制的。
>Replicaset 定義了一個 Pod Template ，實現要創建這樣一個 Pod。

查看一下這個 pod
![[k8s - Controller and Deployment 5.png]]
>[!info]
>我們可以看到 Controlled By這一行，可以發現 `coredns` 是被 `ReplicaSet` 所控制的。

串聯起來他們之間的關係就是: **`Deployment` 控制 `ReplicaSet` 再控制 `coredns`**。

### 創建 Deployment 
我們使用 --dry-run=client 來創建一個 nginx deployment 的 yml檔案再使用`apply` 來創建 ，當然我們也可以直接使用命令的方式創建 Deployment 。
```shell
tai@tai-virtual-machine:~$ kubectl create deployment web --image=nginx:1.14.2 --dry-run=client -o yaml > web.yml
tai@tai-virtual-machine:~$
tai@tai-virtual-machine:~$ more web.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
      - image: nginx:1.14.2
        name: nginx
        resources: {}
status: {}
```

查看Kubernetes 部署的詳細狀況。
![[k8s - Controller and Deployment 6.png]]
>[!info]
>- **Deployment** 負責管理應用的整體部署，確保期望的副本數、更新狀態和可用狀態。
>- **ReplicaSet** 由 Deployment 創建並管理，確保 Pod 副本數量一致。
>- **Pod** 是應用的實際運行單位，由 ReplicaSet 管理，確保運行狀態和可用性。
### 結論
創建一個 Deployment 時，Kubernetes 會自動幫您創建相應的 ReplicaSet 和 Pod。這是 Deployment 控制器的工作方式，確保應用程序能夠按期望的方式部署和運行。

```
+--------------+
|  Deployment  |           用戶創建 Deployment
+--------------+
        |
        v
+--------------+          Deployment 控制器創建 ReplicaSet
|  ReplicaSet  |
+--------------+
        |
        v
+--------------+          ReplicaSet 創建和管理 Pod
|      Pod     |
+--------------+
```
## Deployment 之 Pod Failure
### Pod Failures
重新排程並建立一個新的 Pod
#### 驗證
創建一個 deployments
```shell
tai@tai-virtual-machine:~$ kubectl create deployment web --image=nginx
```

更改 ReplicaSet ，這邊有兩種方式可以更改
```shell
kubectl edit deployments.apps web 

or

kubectl scale deployment web --replicas 3
```
>[!info]
>- `kubectl edit deployments.apps web`: 編輯 Kubernetes 集群中名為 `web` 的 Deployment 資源的 YAML 配置文件。
>- `kubectl scale deployment web --replicas 3`: 使用命令方式調整k8s中的deployment資源裡面名為web.yml中的replicas。

創建成功
```shell
tai@tai-virtual-machine:~$ kubectl get pods
NAME                  READY   STATUS    RESTARTS   AGE
web-76fd95c67-5d7pp   1/1     Running   0          8m58s
web-76fd95c67-7smsf   1/1     Running   0          19s
web-76fd95c67-hcn6m   1/1     Running   0          19s
```

我們來驗證一下刪除其中一個，首先先來監聽我們的 kubectl
```shell
kubectl get events -w &
```
##### 刪除 Pod 
刪除
```shell
kubectl delete pod web
```
![[k8s - Controller and Deployment 7.png]]
>[!info]
>我們可以發現，如果 deployment 裡面出現減少情況，那麼 deployment controller 和它下面的 ReplicaSet controller 會自動幫你馬上創建一個新的 pods ，來達到我們期望的狀態。

##### 更改 Labels
查看 labels
```shell
tai@tai-virtual-machine:~$ kubectl get pods --show-labels
NAME                  READY   STATUS    RESTARTS   AGE     LABELS
web-76fd95c67-7smsf   1/1     Running   0          8m54s   app=web,pod-template-hash=76fd95c67
web-76fd95c67-hcn6m   1/1     Running   0          8m54s   app=web,pod-template-hash=76fd95c67
web-76fd95c67-n68p8   1/1     Running   0          5m47s   app=web,pod-template-hash=76fd95c67
```
>[!info]
>這邊的 app=web 的 labels ，是我們 deployment 的 labels selector ，意思是說 deployment 和 replicaset 通過這個 labels去控制我們當前有多少的 pods。

修改 pods 的 labels 
![[k8s - Controller and Deployment 8.png]]
>[!info]
>上面所說的 deployment 是根據 app=web 的 labels來控制有多少個的 pods，我們更改了 app=test，他發現變少後自動創建一個。

我們查看剛自動創建pod的詳細資訊，也確實它的 `Controlled By` 是屬於 `ReplicaSet`
![[k8s - Controller and Deployment 9.png]]

那也就是說，我們剛剛更改 `app=test` 的 pod 中，`Controlled By`會不屬於任何人，也確實如此。
![[k8s - Controller and Deployment 10.png]]

那如果我們更改回去呢
![[k8s - Controller and Deployment 11.png]]
>[!info]
>發現它會自動將剛剛所創建的 pod 刪除了 !
### Node Failures
- 瞬間故障: 像是網路問題等等。
- 永久性故障
	- kube-contorller-manager 有一個超時的設置。
	- pod-eviction-timeout （預設5min） 節點如果失聯超過5分鐘，就會觸發在其上運行的Pod的終止和重建。

如果 Ready 條件的狀態在超過 `pod-eviction-timeout`（傳遞給 kube-controller-manager 的一個參數）的時間內保持為 `Unknown` 或 `False`，那麼節點控制器將觸發 API 發起的驅逐操作，將分配給該節點的所有 Pod 驅逐。默認的驅逐超時持續時間為五分鐘。
#### 演示
我們先將 worker2 關機
```shell
tai@tai-virtual-machine:~$ kubectl get pods -o wide
NAME                  READY   STATUS    RESTARTS   AGE   IP            NODE          NOMINATED NODE   READINESS GATES
web-76fd95c67-7smsf   1/1     Running   0          62m   10.244.2.16   k8s-worker2   <none>           <none>
web-76fd95c67-hcn6m   1/1     Running   0          62m   10.244.1.12   k8s-worker1   <none>           <none>
web-76fd95c67-n68p8   1/1     Running   0          59m   10.244.1.13   k8s-worker1   <none>           <none>
tai@tai-virtual-machine:~$ kubectl get nodes
NAME                  STATUS     ROLES           AGE     VERSION
k8s-worker1           Ready      <none>          6d17h   v1.29.2
k8s-worker2           NotReady   <none>          6d17h   v1.29.2
tai-virtual-machine   Ready      control-plane   7d4h    v1.29.2
```
>[!info]
>此時 pod 還會存活，但這邊 k8s-worker2 狀態已經是 notReady 了，這是處在暫時性的失聯情況，需要等到 5 分鐘後，他才會被 terminating ，但要注意這時 pod 存在，只是狀態為 terminating 而已。

而如果這時將 k8s-worker2 啟動
![[k8s - Controller and Deployment 12.png]]
>[!info]
>我們會發現 terminating 的 pod 被刪除了 ! 
## Deployment Update Strategy: RollingUpdate
`Deployment` 控制器允許用戶聲明應用的期望狀態，並自動管理應用的部署和擴展。滾動更新（RollingUpdate）是 Kubernetes `Deployment` 控制器的一種更新策略，旨在**逐步替換舊版本的 Pods**，以確保在應用程式更新過程中保持服務的可用性和穩定性。
### 演示
監聽 k8s 
```
kubectl get events -w
```
創建一個部署
```shell
kubectl create deployment web --image=nginx:1.14 --replicas 3
```

更新image，或是使用 yml 文件修改內部的image
```shell
kubectl set image deployment/web nginx=nginx:1.14.2

or

kubectl edit deployments.apps web 
```

查看 log 
![[k8s - Controller and Deployment 13.png]]
>[!info]
我們可以發現它確實是 rollingupdate 的方式去更新 pods 。
## Rolling Back ( 返回之前版本)
查看rollout history
```shell
kubectl rollout history deployment web
```
![[k8s - Controller and Deployment 14.png]]
>[!info]
>- 我們可以發現這邊的 Change-Cause 是 None，通常是因為在創建或更新部署時沒有添加變更註釋。
>- Revision: 數字越大代表越新創建。

我們可以使用 `annotate` 來**手動添加變更原因註釋**，ps. `--record` 已棄用。
```shell
kubectl annotate deployment/nginx kubernetes.io/change-cause="Updated nginx to 1.17"
```

如果都為 None 的話，我們可以查看 revision history ，指定相對應的版本。
```shell
kubectl rollout history deployment web --revision=1
```
![[k8s - Controller and Deployment 15.png]]
>[!info]
>就可以發現 image 是屬於什麼版本的了 !

部署回滾到指定的修訂版本
```shell
kubectl rollout undo deployment web --to-revision=1
```
>[!info]
>- **kubectl**：Kubernetes 命令行工具，用於與 Kubernetes 叢集進行互動。
>- **rollout**：這是 `kubectl` 的一個子命令，用於管理和檢查部署的滾動更新狀態。
>- **undo**：這個子命令用於撤銷一個應用程序的最近或特定的修訂版本變更。
>- **deployment web**：指定要操作的資源類型和名稱，這裡是 `web` 部署。
>- **--to-revision=1**：選擇器，用於指定回滾到哪個修訂版本，這裡指定為修訂版本 `1`。
## Restaring Deployment
```shell
kubectl rollout restart deployment web
```
>[!info]
>這邊的重新啟動，其實並不是真正重新啟動，而是跟 更新 Deployment 的方式一樣。

![[k8s - Controller and Deployment 16.png]]
>[!info]
>- 可以看到 strategy 有兩種方式可以運行，1、Recreate  2、RollingUpdate
>- deployment.spec.strategy : 這邊為 預設創建 deployment yml 檔案中的spec.strategy。

我們這邊改為 Recreate 來看看效果
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  strategy:
    type: Recreate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
      - image: nginx:1.14
        name: nginx
        resources: {}
status: {}
```
![[k8s - Controller and Deployment 17.png]]
>[!info]
>可以發現它直接 scaled down 至 0 了

