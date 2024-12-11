---
Date: 2024-07-24
tags:
  - k8s
  - devops
---
在 Kubernetes 中，Service 是一種抽象，提供了穩定的網絡端點，使得其他服務或應用可以通過這個端點訪問後端的 Pod。這裡，我們重點介紹最基本的 Service 類型：ClusterIP。
#### 永久性端點訪問
- 為客戶端提供持久性的端點，增強 Pod 的短暫性。
- 提供持久的虛擬 IP 和 DNS。
- 負載均衡至後端 Pod。
- 在 Pod 控制器操作期間自動更新。
#### Service 的工作原理
- Services 使用 Labels 和 Selectors 來匹配 Pods。
- 在 Service 中創建並註冊 Endpoints（Pod 的 IP 和端口配對）。
- 在 Node 上的 kube-proxy 中使用 iptables 實現。
- kube-proxy 監視 API Server 和 Endpoints。
## Service: ClusterIP
### ClusterIP 簡介
ClusterIP 是 Kubernetes 中的默認 Service 類型。當你創建一個 ClusterIP 類型的 Service 時，Kubernetes 會自動分配一個僅在叢集內部可訪問的 IP 地址（即 ClusterIP）。這個 IP 地址使得其他 Pod 可以通過這個統一的入口來訪問該 Service 所代表的一組 Pod。
### ClusterIP 的特性
1. **內部訪問**：
    - ClusterIP 僅在 Kubernetes 叢集內部可見。這意味著只有叢集內的 Pod 和節點可以通過這個 IP 地址來訪問該服務。
2. **自動負載均衡**：
    - ClusterIP 會自動負載均衡流量到後端的 Pod。這些 Pod 通常是通過 Label Selector 來指定的。
3. **穩定的訪問入口**：
    - ClusterIP 提供了一個穩定的網絡入口，即使後端的 Pod 發生變化（如擴展或縮減），ClusterIP 仍然保持不變。
### ClusterIP 的工作原理
1. **定義 Service**：
    - 你需要創建一個 Service 資源，並指定類型為 ClusterIP。
2. **分配 IP 地址**：
    - Kubernetes 會自動分配一個 ClusterIP 地址，這個地址僅在叢集內部有效。
3. **配置 Endpoint**：
    - Kubernetes 會自動管理這個 Service 的 Endpoint，即與這個 Service 關聯的一組 Pod。
#### 實驗室 - 創建一個 Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: hello-world
        image: xiaopeng163/hello-app:1.0
        ports:
        - containerPort: 8080
```
#### Service 類型 - ClusterIP（預設）
- 當應用程式不需要被 `Cluster` 外部訪問時，會分配一個內部的 Cluster IP。

```shell
tai@tai-virtual-machine:~$  kubectl expose deployment hello-world --port=80 --target-port=8080
service/hello-world exposed
tai@tai-virtual-machine:~$ kubectl get svc
NAME          TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
hello-world   ClusterIP   10.96.77.44   <none>        80/TCP    4s
kubernetes    ClusterIP   10.96.0.1     <none>        443/TCP   39d
tai@tai-virtual-machine:~$ curl 10.96.77.44
Hello, world!
Version: 1.0.0
Hostname: hello-world-789db8d56-n76v6
tai@tai-virtual-machine:~$ curl 10.96.77.44
Hello, world!
Version: 1.0.0
Hostname: hello-world-789db8d56-lzf7g
tai@tai-virtual-machine:~$ curl 10.96.77.44
Hello, world!
Version: 1.0.0
Hostname: hello-world-789db8d56-lzf7g
tai@tai-virtual-machine:~$
```
#### 取得 Service 的 YAML 格式
```shell
tai@tai-virtual-machine:~$ kubectl get svc hello-world -o yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2024-07-24T04:12:29Z"
  name: hello-world
  namespace: default
  resourceVersion: "2221696"
  uid: cd195d21-3d37-4262-bd03-742dc94b5dce
spec:
  clusterIP: 10.96.77.44
  clusterIPs:
  - 10.96.77.44
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: hello-world
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}
tai@tai-virtual-machine:~$
```
#### 了解如何運行
在每個節點上，都有一個名為 `kube-proxy` 的組件。這個組件負責管理 Kubernetes 預先定義好的一些 `iptables` 規則，這些規則用於處理和路由網絡流量。

```shell
kubectl get pods -A -o wide
```
![[K8s - Service 1.png]]
#### 深入理解 iptables 規則
```shell
tai@tai-virtual-machine:~$ sudo iptables -t nat -L KUBE-SERVICES -n  | column -t
[sudo] password for tai:
Chain                      KUBE-SERVICES  (2   references)

target                     prot           opt  source       destination

KUBE-SVC-TCOU7JCQXEZGVUNU  udp            --   0.0.0.0/0    10.96.0.10   /*  kube-system/kube-dns:dns      cluster  IP          */

KUBE-SVC-ERIFXISQEP7F7OF4  tcp            --   0.0.0.0/0    10.96.0.10   /*  kube-system/kube-dns:dns-tcp  cluster  IP          */

KUBE-SVC-JD5MR3NA4I4DYORP  tcp            --   0.0.0.0/0    10.96.0.10   /*  kube-system/kube-dns:metrics  cluster  IP          */

KUBE-SVC-DZ6LTOHRG6HQWHYE  tcp            --   0.0.0.0/0    10.96.77.44  /*  default/hello-world           cluster  IP          */

KUBE-SVC-NPX46M4PTMTKRN6Y  tcp            --   0.0.0.0/0    10.96.0.1    /*  default/kubernetes:https      cluster  IP          */

KUBE-NODEPORTS             all            --   0.0.0.0/0    0.0.0.0/0    /*  kubernetes                    service  nodeports;  NOTE:  this  must  be  the  last  rule  in  this  chain  */  ADDRTYPE  match  dst-type  LOCAL
tai@tai-virtual-machine:~$
```
![[K8s - Service 2.png]]
#### 清理和刪除 Service
```shell
kubectl delete svc hello-world
```
## Service: NodePort
- 使 Service 可通過每個 Node 上的一個靜態端口訪問。
- 為每個節點分配一個靜態端口，使**外部流量**能夠通過該端口訪問 Service。

我們可以使用 `10.106.69.172:8080` 、`127.0.0.1:30794` 來訪問，這個地址其他 `pod` 之間也是可以相互進行通訊。如果想要讓本地也可以使用我們可以查看該 `pod` 上面的`local ip` 來進行通訊。

```shell
tai@tai-virtual-machine:~$ kubectl expose deployment hello-world  --target-port=8080 --type=NodePort
service/hello-world exposed
tai@tai-virtual-machine:~$ kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
hello-world-789db8d56-lzf7g   1/1     Running   0          96m
hello-world-789db8d56-n76v6   1/1     Running   0          102m
tai@tai-virtual-machine:~$ kubectl get svc
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
hello-world   NodePort    10.106.69.172   <none>        8080:30794/TCP   12s
kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP          39d
tai@tai-virtual-machine:~$ curl 10.106.69.172:8080
Hello, world!
Version: 1.0.0
Hostname: hello-world-789db8d56-n76v6
tai@tai-virtual-machine:~$ curl 127.0.0.1:30794
Hello, world!
Version: 1.0.0
Hostname: hello-world-789db8d56-lzf7g
```

使用 `ip a` 來查看網路介面卡，使用 `ens33` 本地的網路介面卡，來通訊，也就是可以在本地網頁上輸入 `192.168.118.129:30794`

![[K8s - Service 3.png]]

![[K8s - Service 4.png]]
#### 深入理解 iptables 規則
##### 查看 PREROUTING 鏈
![[K8s - Service 5.png]]
###### 查看 KUBE-SERVICES 鏈
- `KUBE-SERVICES` 鏈定義了所有服務的 IP 和端口映射，包括內部和外部的訪問規則。
- `KUBE-SVC-DZ6LTOHRG6HQWHYE` 規則指向 `hello-world` 服務的 Cluster IP `10.106.69.172`。

![[K8s - Service 6.png|875]]
###### 查看 KUBE-NODEPORTS 鏈
- `KUBE-NODEPORTS` 鏈包含所有 NodePort 服務的規則，使服務可以通過每個 Node 的靜態端口訪問。

![[K8s - Service 7.png]]
###### 查看 KUBE-EXT-DZ6LTOHRG6HQWHYE 鏈
- `KUBE-EXT-DZ6LTOHRG6HQWHYE` 鏈處理外部流量到 `hello-world` 服務的轉發。
- `KUBE-MARK-MASQ` 規則確保外部流量經過服務時的地址偽裝（masquerade）。

![[K8s - Service 8.png|950]]
###### 查看 KUBE-SVC-DZ6LTOHRG6HQWHYE 鏈
- `KUBE-SVC-DZ6LTOHRG6HQWHYE` 鏈處理從 Cluster IP 到 Pod 的流量轉發。
- `KUBE-MARK-MASQ` 規則確保非叢集內的流量經過服務時的地址偽裝。
- `KUBE-SEP-IKXDPMZZPVXN3KBM` 和 `KUBE-SEP-EFITTPEGYQM3635G` 規則將流量隨機轉發到不同的 Pod，以實現負載均衡。

![[K8s - Service 9.png|850]]
## Service: LoadBalancer
在 Kubernetes 中，LoadBalancer 是一種 Service 類型，允許通過雲提供商的負載均衡器功能來使服務對外部訪問變得可用。這意味著服務將獲得一個外部 IP 地址，並且所有來自該地址的流量將被負載均衡到 Kubernetes 集群內的後端 Pod。主要的雲提供商（如 GCP、AWS、Azure 和 OpenStack）都提供這種功能。
#### LoadBalancer 的特性
1. **外部訪問**：
    - LoadBalancer 類型的 Service 獲得一個外部 IP 地址，使得集群外部的客戶端可以訪問這個服務。
2. **自動負載均衡**：
    - 負載均衡器會自動將進來的流量分配到後端的 Pod 上，確保高可用性和可靠性。
3. **雲提供商集成**：
    - LoadBalancer Service 依賴於雲提供商的負載均衡器功能。因此，這種服務類型主要在雲環境中使用。
#### 在 Azure Kubernetes 集群中使用 LoadBalancer
以下是在 Azure Kubernetes 集群中配置和使用 LoadBalancer 的步驟：
### 1. 準備工作
首先，確保你的 Kubernetes 節點處於運行狀態並且已經配置好環境。
### 2. 創建 Deployment
創建一個簡單的 Deployment，使用 `hello-app` 容器映像（如果無法訪問 Google，請使用 `xiaopeng163/hello-app:1.0`）。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: hello-world
        image: xiaopeng163/hello-app:1.0
        ports:
        - containerPort: 8080
```

```shell
kubectl apply -f hello-world-deployment.yaml
```
### 3. 確認 Deployment 和 Pod 狀態
```shell
$ kubectl get deployments.apps
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
hello-world   1/1     1            1           5m50s

$ kubectl get pods -o wide
NAME                           READY   STATUS    RESTARTS   AGE    IP           NODE                               NOMINATED NODE   READINESS GATES
hello-world-54575d5b77-cp75f   1/1     Running   0          6m4s   10.244.1.5   aks-services-38564575-vmss000003   <none>           <none>
```
### 4. 創建 LoadBalancer Service
```shell
$ kubectl expose deployment hello-world --port=80 --target-port=8080 --type=LoadBalancer
service/hello-world exposed
```
### 5. 確認 Service 狀態
```shell
$ kubectl get service
NAME          TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)        AGE
hello-world   LoadBalancer   10.0.202.198   23.97.235.50   80:31468/TCP   29s
```
### 6. 訪問外部 IP 地址
打開瀏覽器訪問 `http://23.97.235.50/`，你應該能夠看到 `hello-app` 的響應：

```shell
Hello, world!
Version: 1.0.0
Hostname: hello-world-xxxxx
```