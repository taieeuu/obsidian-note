---
Date: 2024-07-27
---
>[!info]
>Nginx Ingress Controller: [https://github.com/kubernetes/ingress-nginx/blob/main/docs/deploy/index.md](https://github.com/kubernetes/ingress-nginx/blob/main/docs/deploy/index.md)

---
Ingress Controller 是 Kubernetes 中負責實際處理和執行 Ingress 資源所定義的路由規則的組件。它監控 Kubernetes API Server 中的 Ingress 資源，並根據這些資源的規則配置相應的反向代理或負載均衡器，將外部流量路由到集群內部的服務。
### Ingress Controller 的功能
1. **監控 Ingress 資源**：
    - Ingress Controller 持續監控 Kubernetes API Server 中的 Ingress 資源，當有新的 Ingress 資源創建、修改或刪除時，Ingress Controller 會自動更新其配置。
2. **配置反向代理/負載均衡器**：
    - Ingress Controller 根據 Ingress 資源中定義的路由規則，配置相應的反向代理或負載均衡器（例如 NGINX、Traefik、HAProxy 等），以處理進入的 HTTP/HTTPS 請求。
3. **處理 SSL/TLS**：
    - Ingress Controller 可以管理 SSL/TLS 證書，實現 HTTPS 流量的終止，確保數據傳輸的安全性。
4. **負載均衡和高可用性**：
    - Ingress Controller 提供應用層的負載均衡，根據配置的策略將流量分配到不同的後端服務，提升應用的可用性和擴展性。
### 常見的 Ingress Controller
以下是一些常見的 Ingress Controller：
1. **NGINX Ingress Controller**：
    - NGINX Ingress Controller 是最流行的 Ingress Controller 之一，它基於 NGINX 反向代理服務器，提供了強大的負載均衡和路由功能。
2. **Traefik**：
    - Traefik 是一個現代 HTTP 反向代理和負載均衡器，它原生支持 Kubernetes，能夠自動監控和配置 Ingress 資源。
3. **HAProxy Ingress Controller**：
    - HAProxy Ingress Controller 基於 HAProxy 反向代理和負載均衡器，提供高效能的流量處理和負載均衡。
4. **Istio Ingress Gateway**：
    - Istio 是一個開源服務網格，它的 Ingress Gateway 提供了高級的流量管理和安全功能。
### 部署 Ingress Controller
從 GitHub 上的官方存儲庫中下載並應用了一個 YAML 文件，該文件包含了部署 NGINX Ingress Controller 所需的所有 Kubernetes 資源。

```shell
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.1/deploy/static/provider/cloud/deploy.yaml
```
### 檢查 Pod 狀態
顯示當前 `default` 命名空間中沒有資源（Pod）。這是因為你剛剛部署的 Ingress Controller 在一個單獨的命名空間 `ingress-nginx` 中。

```shell
kubectl get pods
```
### 檢查命名空間
顯示了集群中所有的命名空間，包括新創建的 `ingress-nginx` 命名空間。可以看到`ingress-nginx` 激活且為 `44s` 前創建。

```shell
tai@tai-virtual-machine:~$ kubectl get namespaces
NAME              STATUS   AGE
default           Active   42d
demo              Active   40d
ingress-nginx     Active   44s
kube-flannel      Active   41d
kube-node-lease   Active   42d
kube-public       Active   42d
kube-system       Active   42d
```
### 檢查 Ingress-Nginx 命名空間中的所有資源
顯示了 `ingress-nginx` 命名空間中的所有資源，包括 Pod、服務（Service）、部署（Deployment）、ReplicaSet 和任務（Job）。

```shell
kubectl get all --namespace ingress-nginx
```
![[K8s - Ingress Controller 1.png]]
#### 資源的詳細說明：
##### Pod
- `pod/ingress-nginx-admission-create-7k52t` 和 `pod/ingress-nginx-admission-patch-g5tbq`：這些是完成的任務 Pod，用於配置 Ingress 控制器的驗證 Webhook。
- `pod/ingress-nginx-controller-6f4cd55bd6-lh25v`：這是 Ingress 控制器的主 Pod，處於運行狀態。
##### 服務（Service）
- `service/ingress-nginx-controller`：這是 LoadBalancer 類型的服務，負責將外部流量路由到 Ingress 控制器。因為在本地環境中運行，所以 EXTERNAL-IP 處於 `<pending>` 狀態。
- `service/ingress-nginx-controller-admission`：這是 ClusterIP 類型的服務，用於 Ingress 控制器的驗證 Webhook。
##### 部署（Deployment）
- `deployment.apps/ingress-nginx-controller`：這是 Ingress 控制器的部署資源，管理 Pod 的創建和更新。當前有 1 個副本，並且處於運行狀態。
#### ReplicaSet
- `replicaset.apps/ingress-nginx-controller-6f4cd55bd6`：這是由 Deployment 管理的 ReplicaSet，確保有 1 個 Pod 處於運行狀態。
##### 任務（Job）
- `job.batch/ingress-nginx-admission-create` 和 `job.batch/ingress-nginx-admission-patch`：這些任務負責創建和修補 Ingress 控制器的驗證 Webhook，並已經完成。

**補充**
`ingress-nginx-controller` 已經成功部署並處於運行狀態。然而，由於你的服務類型是 `LoadBalancer`，並且 `EXTERNAL-IP` 處於 `<pending>` 狀態，所以目前你無法直接通過 `curl` 命令來訪問它。

在本地環境中運行 Kubernetes 時，`LoadBalancer` 類型的服務通常無法獲得外部 IP，**因為這需要雲服務提供商的負載均衡器支持（如 AWS ELB、GCP GLB 等）**。在本地環境中，可以使用 NodePort 或將 Minikube 的 LoadBalancer 設置為一個能夠模擬外部 IP 的解決方案。