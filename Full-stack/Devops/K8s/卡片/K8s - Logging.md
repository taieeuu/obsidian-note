---
Date: 2024-07-30
tags:
---
---
>[!info]
>[https://kubernetes.io/docs/concepts/cluster-administration/logging/](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
### 前置設定
```shell
tai@tai-virtual-machine:~$ kubectl create deployment hellow-world --image nginx
deployment.apps/hellow-world created
tai@tai-virtual-machine:~$ kubectl get pods
NAME                            READY   STATUS    RESTARTS   AGE
hellow-world-5f94bc9985-5nl6w   1/1     Running   0          82s
tai@tai-virtual-machine:~$ kubectl expose deployment hellow-world --port=80 --target-port=80
service/hellow-world exposed
tai@tai-virtual-machine:~$ kubectl get service
NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
hellow-world   ClusterIP   10.97.235.198   <none>        80/TCP    59s
kubernetes     ClusterIP   10.96.0.1       <none>        443/TCP   44d
tai@tai-virtual-machine:~$ curl 10.97.235.198
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
### 訪問日誌數據 - Pods和容器
使用 `kubectl logs` 來訪問Pods和容器的日誌數據：

```shell
$ kubectl logs $POD_NAME $ kubectl logs $POD_NAME -c $CONTAINER_NAME $ kubectl logs $POD_NAME --all-containers $ kubectl logs --selector app=demo $ kubectl logs -f $POD_NAME   # 追蹤最新日誌 $ kubectl logs $POD_NAME --tail 5   # 獲取最後5條日誌
```

如果API服務器不可用，則需要手動到容器所在的節點查看容器日誌：

```shell
crictl --runtime-endpoint unix:///run/containerd/containerd.sock logs $CONTAINER_ID
```

or

```shell
tail /var/log/containers/$CONTAINER_NAME_$CONTAINER_ID
```
### 訪問日誌數據 - 節點
##### 節點上有兩個關鍵的組件：`kubelet` 和 `kube-proxy`。
檢查 `kubelet` 服務的狀態：

```shell
systemctl status kubelet.service  # 檢查服務狀態
```

使用 `journalctl` 來檢查 `kubelet` 的日誌：

```shell
journalctl -u kubelet.service
journalctl -u kubelet.service | grep -i ERROR
journalctl -u kubelet.service --since today --no-pager
```
### 訪問日誌數據 - 控制平面
##### 控制平面可以作為Pods運行或使用 `systemd` 運行。
作為Pods運行時，使用 `kubectl logs`：

```shell
kubectl logs -n kube-system $POD_NAME
```

使用 `systemd` 運行時，檢查 `kubelet` 服務的狀態：

```shell
systemctl status kubelet.service  # 檢查服務狀態
journalctl -u kubelet.service
```
### 集中式日誌解決方案
![[K8s - Logging 1.png]]