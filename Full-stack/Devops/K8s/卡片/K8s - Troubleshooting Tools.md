---
Date: 2024-07-31
---
---
在 Kubernetes (K8s) 環境中，除了監控工具外，還有一些常用的故障排除工具。
#### kubectl logs
查看名為 `my-pod` 的 Pod 中容器的日誌。

```shell
kubectl logs my-pod
```
    
查看特定容器的日誌
```shell
kubectl logs my-pod -c my-container
```
#### kubectl events
查看 Kubernetes 集群中所有資源的事件。

```shell
kubectl get events
```
#### systemctl
查看 Docker 服務的狀態。

```shell
sudo systemctl status docker
```

啟動、停止和重啟 Docker 服務

```shell
sudo systemctl start docker sudo systemctl stop docker sudo systemctl restart docker
```
#### journalctl
查看 Docker 服務的日誌

```shell
sudo journalctl -u docker
```
    
查看 Kubelet 服務的日誌
```shell
sudo journalctl -u kubelet
```
#### System Logs
查看系統日誌檔案 `/var/log/syslog`。

```shell
tail -f /var/log/syslog
```

查看內核環境信息的日誌

```shell
dmesg
```
    
   