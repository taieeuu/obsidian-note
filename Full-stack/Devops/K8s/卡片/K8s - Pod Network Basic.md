---
Date: 2024-07-22
tags:
  - k8s
  - devops
---
### Pod Networking 基礎
#### Pod 共享網絡命名空間
在 Kubernetes 中，Pod 內的容器共享一個網絡命名空間，這意味著它們可以通過 `localhost` 進行通信。
#### 安裝必要的包（以 Ubuntu 為例）
在集群的所有節點上安裝以下軟件包：

```shell
$ sudo apt install bridge-utils net-tools
```
#### 容器間通信（同一 Pod）
創建測試 Pod：

**dev_pods.yml**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  nodeName: 'k8s-worker1' # 指定節點，請根據實際情況修改
  containers:
  - name: container1
    image: xiaopeng163/net-box
    command: ["sh", "-c", "while true; do echo $(date) >> /tmp/index.html; sleep 60; done"]
  - name: container2
    image: xiaopeng163/net-box
    command: ["sh", "-c", "while true; do echo $(date) >> /tmp/index.html; sleep 60; done"]

```

部署該 Pod：

```shell
kubectl apply -f dev_pods.yml
```

獲取 Pod 所在節點和 IP 地址：

```shell
kubectl get pods -o wide
```

輸出示例：

```shell
NAME     READY   STATUS    RESTARTS   AGE   IP           NODE          NOMINATED NODE   READINESS GATES
my-pod   2/2     Running   0          9s    10.244.1.4   k8s-worker1   <none>           <none>
```

Pod 的兩個容器共享一個網絡命名空間，因此它們可以通過 `localhost` 進行通信，IP 地址為 `10.244.1.4`。

Pod 的兩個容器共享一個網絡命名空間，因此它們可以通過 `localhost` 進行通信，IP 地址為 `10.244.1.4`。

在 k8s-worker1 節點上驗證：

```shell
# 獲取容器 ID
$ sudo ctr --namespace=k8s.io container ls | grep net-box

# 獲取每個容器的網絡命名空間
$ sudo ctr --namespace=k8s.io container info <container_id> | jq '.Spec.linux.namespaces'

# 檢查網絡命名空間的 IP 地址
$ sudo nsenter -t <pid> -n ip addr
```

清理測試 Pod：
```shell
kubectl delete -f dev_pods.yaml
```
#### Pod 之間通信（單節點）
創建測試 Pod：

**dev_pods.yml**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod1
spec:
  nodeName: 'k8s-worker1' # 指定節點，請根據實際情況修改
  containers:
  - name: pod1
    image: xiaopeng163/net-box
    command: ["sh", "-c", "while true; do echo $(date) >> /tmp/index.html; sleep 60; done"]
---
apiVersion: v1
kind: Pod
metadata:
  name: mypod2
spec:
  nodeName: 'k8s-worker1' # 指定節點，請根據實際情況修改
  containers:
  - name: pod2
    image: xiaopeng163/net-box
    command: ["sh", "-c", "while true; do echo $(date) >> /tmp/index.html; sleep 60; done"]
```

部署該 Pod：

```shell
kubectl apply -f dev_pods.yml
```
#### Pod 之間通信（多節點）
創建測試 Pod：

```shell
apiVersion: v1
kind: Pod
metadata:
  name: mypod1
spec:
  containers:
  - name: pod1
    image: xiaopeng163/net-box
    command: ["sh", "-c", "while true; do echo $(date) >> /tmp/index.html; sleep 60; done"]
---
apiVersion: v1
kind: Pod
metadata:
  name: mypod2
spec:
  containers:
  - name: pod2
    image: xiaopeng163/net-box
    command: ["sh", "-c", "while true; do echo $(date) >> /tmp/index.html; sleep 60; done"]
```
#### 結論
我們無論是在同一個 Pod 內的容器之間，還是不同 Pod 之間，都能夠通過特定的配置來實現通信。
1. **同一 Pod 內的容器通信**：
    - 同一 Pod 內的容器共享一個網絡命名空間，因此它們可以使用 `localhost` 進行通信。
2. **不同 Pod 之間的通信（單節點）**：
    - 不同 Pod 之間的通信需要通過 Pod 的 IP 地址來進行。
    - 在單節點環境中，不同 Pod 通信相對簡單，因為它們都在同一個物理節點上。
3. **不同 Pod 之間的通信（多節點）**：
    - 當 Pod 分佈在不同的節點上時，通信會稍微複雜一些，需要配置跨節點的網絡路由。
    - Kubernetes 通常使用 CNI（Container Network Interface）插件來管理跨節點的網絡通信，確保不同節點上的 Pod 之間能夠正常通信。
此外，**我們也可以在其他的 worker 節點上進行通信驗證，確保跨節點的 Pod 之間可以互相 ping 通。**