---
Date: 2024-07-28
---
---
繼承上一篇 [[K8s - etcd data backup]]

在開始之前，我們需要先刪除 `my-sql` 資料

```shell
tai@tai-virtual-machine:~$ kubectl delete secrets mysql-secret
secret "mysql-secret" deleted
```
### 恢復步驟
###### 恢復 etcd 快照：
![[K8s - etcd data restore 1.png]]
###### 備份現有 etcd 數據
```shell
tai@tai-virtual-machine:~$ sudo mv /var/lib/etcd /var/lib/etcd.OLD
```
###### 複製恢復的數據到 etcd 數據目錄：
恢復完數據接下來我們會需要重新啟動，這樣`pod`才會重新使用我們的數據。

```shell
tai@tai-virtual-machine:~$ sudo ETCDCTL_API=3 etcdctl --write-out=table \
  snapshot status /var/lib/dat-backup.db
Deprecated: Use `etcdutl snapshot status` instead.

+----------+----------+------------+------------+
|   HASH   | REVISION | TOTAL KEYS | TOTAL SIZE |
+----------+----------+------------+------------+
| 28158da8 |  2770309 |       1057 |     3.3 MB |
+----------+----------+------------+------------+
```
###### 停止 etcd 容器：
找到容器 ID：

![[K8s - etcd data restore 2.png]]

停止容器：
```shell
tai@tai-virtual-machine:~$ sudo crictl --runtime-endpoint unix:///run/containerd/containerd.sock stop 1847b07ddf361
1847b07ddf361
```
### 驗證
```shell
tai@tai-virtual-machine:~$ kubectl get secrets

NAME           TYPE                DATA   AGE
mysql-secret   Opaque              3      62m
test-tls       kubernetes.io/tls   2      24h
```

**注意**
執行下列指令會發生已棄用，`Deprecated: Use `etcdutl snapshot restore` instead.` ，但實際上還是可以運行

```shell
sudo ETCDCTL_API=3 etcdctl snapshot restore /var/lib/dat-backup.db
```

不過如果需要解決它的話

```shell
cd etcd-v3.5.3-linux-amd64

mv etcdutl /usr/local/bin
```

還原指令需指定

```shell
sudo ETCDCTL_API=3 etcdutl snapshot restore /var/lib/dat-backup.db --data-dir /var/lib/etcd
```