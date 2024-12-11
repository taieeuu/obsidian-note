---
Date: 2024-07-31
---
---
- Server online
- Network reachability
- systemd
- container runtime
- kubelet
- static pod manifest

static pod config: /var/lib/kubelet/config.yaml `staticPodPath`
### Control Plane
```shell
# check kube-system pods
kubectl get pods --namespaces kube-system

# user container runtime
crictl --runtime-endpoint unix:///run/containerd/containerd.sock ps

# check static pod configuration
sudo more /var/lib/kubelet/config.yaml

sudo ls -l /etc/kubernetes/manifests
```
### Workloads
##### 檢查資源
獲取 Kubernetes 資源的詳情。
```sh
kubectl get <resource> <name> -n <namespace>
```
##### 描述資源
查看資源的詳細描述。
```sh
kubectl describe <resource> <name> -n <namespace>
```
##### 獲取事件
查看特定命名空間中的所有事件。

```sh
kubectl get events -n <namespace>
```
##### 查看日誌
查看 Pod 中容器的日誌。

```sh
kubectl logs <pod-name> -n <namespace>
```