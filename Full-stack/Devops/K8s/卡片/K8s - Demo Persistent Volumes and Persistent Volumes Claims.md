---
Date: 2024-07-21
---
## Demo Static Volume Provisionling
#### 1. 創建 PersistentVolume (PV)
首先，定義並創建一個 PersistentVolume，這裡使用 NFS 作為存儲後端。

這邊使用的 `Reclaim Policy` 是 `Retain`，如果刪除 `pvc`時，他將會保留數據，需手動刪除。

**pv.yml**
```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 4Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  nfs:
    server: <nfs-ip>
    path: "/export/volumes/pod"
```

使用 `kubectl` 命令應用這個配置：

```shell
tai@tai-virtual-machine:~$ kubectl apply -f pv.yml
persistentvolume/pv-nfs created
```

查看 PersistentVolume 的狀態：

```shell
tai@tai-virtual-machine:~$ kubectl get pv
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
pv-nfs   4Gi        RWX            Retain           Available                          <unset>                          2s
```
#### 2. 創建 PersistentVolumeClaim (PVC)
接下來，定義並創建一個 PersistentVolumeClaim。

**pvc.yml**
```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
```

使用 `kubectl` 命令應用這個配置：

```shell
tai@tai-virtual-machine:~$ kubectl apply -f pvc.yml
persistentvolumeclaim/pvc-nfs created
```

查看 PersistentVolumeClaim 的狀態：

```shell
tai@tai-virtual-machine:~$ kubectl get pvc
NAME      STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
pvc-nfs   Bound    pv-nfs   4Gi        RWX                           <unset>                 19s
```

##### 那這邊我們的 `pvc` 只申請 `1Gi` 的大小，我們可以再去創建另一個 `pvc` 去應用嗎 ? 
我們使用 `pvc.yml` 複製一份成 `pvc1.yml`
```shell
cp pvc.yml pvc1.yml
```

**pvc1.yml**
```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc1-nfs
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 2Gi
```
##### 為什麼第二個 PVC 處於 Pending 狀態？
在 Kubernetes 中，一個 PersistentVolume 只能綁定到一個 PersistentVolumeClaim。即使 PV 的容量超過第一個 PVC 的需求，它也無法被另一個 PVC 使用。這意味著，即使 pv-nfs 還有剩餘的容量，它也無法與第二個 PVC 綁定。

```shell
tai@tai-virtual-machine:~$ kubectl get pvc
NAME       STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
pvc-nfs    Bound     pv-nfs   4Gi        RWX                           <unset>                 48m
pvc1-nfs   Pending                                                     <unset>                 9s
tai@tai-virtual-machine:~$ kubectl describe pvc pvc1-nfs
Name:          pvc1-nfs
Namespace:     default
StorageClass:
Status:        Pending
Volume:
Labels:        <none>
Annotations:   <none>
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:
Access Modes:
VolumeMode:    Filesystem
Used By:       <none>
Events:
  Type    Reason         Age                From                         Message
  ----    ------         ----               ----                         -------
  Normal  FailedBinding  10s (x2 over 24s)  persistentvolume-controller  no persistent volumes available for this claim and no storage class is set
tai@tai-virtual-machine:~$
```
##### 解決方式
如果需要多個 PVC，可以創建多個 PV，或者使用**動態配置來管理存儲資源**。

上述的介紹是使用 **Static Volume Provisionling** 配置。
#### 3. 在 Pod 中使用 PVC
接著，定義一個 Deployment，並在 Pod 中使用 PVC。

**nginx.yml**
```shell
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      volumes:
      - name: webcontent
        persistentVolumeClaim:
          claimName: pvc-nfs
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
        volumeMounts:
        - name: webcontent
          mountPath: "/usr/share/nginx/html/web-app"
```

使用 `kubectl` 命令應用這個配置：

```shell
kubectl apply -f nginx.yml
```

查看狀態與是否有 `mount` 成功，節點位於 `k8s-worker2` 上，所以要去 `k8s-worker2` 上查看。
```shell
tai@tai-virtual-machine:~$ kubectl get pods -o wide
NAME                   READY   STATUS    RESTARTS   AGE   IP            NODE          NOMINATED NODE   READINESS GATES
web-757898d9d7-fl99f   1/1     Running   0          29s   10.244.2.98   k8s-worker2   <none>           <none>
tai@tai-virtual-machine:~$
```

在 `k8s-worker2` 上查看 `mount` 結果

```shell
tai2@k8s-worker2:~$ mount | grep nfs
nfsd on /proc/fs/nfsd type nfsd (rw,relatime)
192.168.118.131:/export/volumes/pod on /var/lib/kubelet/pods/085f2ac7-47dc-45af-9c03-5c60d47cdfa1/volumes/kubernetes.io~nfs/pv-nfs type nfs4 (rw,relatime,vers=4.2,rsize=524288,wsize=524288,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=192.168.118.130,local_lock=none,addr=192.168.118.131)
tai2@k8s-worker2:~$
```

去NFS server上建立一個文件，具體路徑為：

```shell
tai@nfs-server:/export/volumes/pod$ nano index.html
tai@nfs-server:/export/volumes/pod$ more index.html
hello k8s
tai@nfs-server:/export/volumes/pod$ ls
demo.txt  index.htm
```

創建一個 `service`

```shell
tai@tai-virtual-machine:~$ kubectl expose deployment web --port=80 --type=NodePort
service/web exposed
tai@tai-virtual-machine:~$ kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        36d
web          NodePort    10.101.119.134   <none>        80:31307/TCP   11s
```

測試服務是否運行：

```shell
tai@tai-virtual-machine:~$ curl 127.0.0.1:31307
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

確認 NFS 共享內容：

```shell
tai@tai-virtual-machine:~$ curl 127.0.0.1:31307/web-app/demo.txt
dddddddddddddd
ddddddddddd

tai@tai-virtual-machine:~$ curl 127.0.0.1:31307/web-app/index.html
hello k8s
tai@tai-virtual-machine:~$
```
#### Clean
```shell
$ kubectl delete service web
$ kubectl delete deployments.apps web
$ kubectl delete persistentvolumeclaims pvc-nfs
$ kubectl delete persistentvolume pv-nfs
```