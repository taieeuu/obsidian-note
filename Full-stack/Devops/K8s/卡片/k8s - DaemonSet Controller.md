---
Date: 2024-06-23
tags:
  - k8s
  - devops
---
確保所有或部分Kubernetes叢集節點上運行一個pod。當有新節點加入時，pod也會運行在上面。

默認部署時，會運行在每個節點上，除了 master 節點外，但可使用`tolerations` 來添加在 master 節點上。

常見運用例子
- **kube-proxy**：管理 Kubernetes 集群內部的網路。
- **Log Collectors**：收集節點和應用程序的日誌。
- **Metric Servers**：收集和存儲集群的度量標準。
- **Resource Monitoring Agents**：監控節點的資源使用情況。
- **Storage Daemons**：管理節點上的存儲資源。
### DaemonSet 配置範例
```yml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: hello-ds
spec:
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
        image: nginx:1.14
```

使用 `nodeSelector` 指定節點
```yml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: hello-ds
spec:
  selector:
    matchLabels:
      app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      nodeSelector:
        node: hello-world
      containers:
      - name: hello-world
        image: nginx:1.14
```
>[!info]
>- `nodeSelector` 用於指定 Pod 應該運行的節點。這是通過節點上的標籤來實現的。在上述例子中，只有帶有標籤 `node=hello-world` 的節點才會運行 `hello-ds` DaemonSet 的 Pod。
## Update Strategy 
- RollingUpdate
- OnDelete
