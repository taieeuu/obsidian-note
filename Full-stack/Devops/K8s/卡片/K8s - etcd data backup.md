---
Date: 2024-07-28
---
---
>[!info]
>etcd文檔 : [https://etcd.io/docs/](https://etcd.io/docs/)
### etcd 數據備份與恢復
etcd 數據的備份可以通過 `etcdctl` 命令行工具創建快照(snapshot)來進行。備份產生的數據應盡快複製到集群外的一個安全地方保存。

對於使用 kubeadm 搭建的 Kubernetes 集群，etcd 運行在一個 pod 中，數據存儲在 `/var/lib/etcd` 目錄下，該目錄通過 hostPath 掛載到 master 節點上。
#### 備份步驟
###### 獲取 etcd 數據掛載信息
```shell
tai@tai-virtual-machine:~/etcd-v3.5.3-linux-amd64$ kubectl get  pod --namespace kube-system etcd-tai-virtual-machine -o jsonpath='{.spec.containers[0].volumeMounts}' | jq
[
  {
    "mountPath": "/var/lib/etcd",
    "name": "etcd-data"
  },
  {
    "mountPath": "/etc/kubernetes/pki/etcd",
    "name": "etcd-certs"
  }
]
```
###### 查看 etcd 數據目錄結構：
```shell
tai@tai-virtual-machine:~$ sudo tree /var/lib/etcd/
/var/lib/etcd/
└── member
    ├── snap
    │   ├── 0000000000000006-00000000002f75a8.snap
    │   ├── 0000000000000006-00000000002f9cb9.snap
    │   ├── 0000000000000006-00000000002fc3ca.snap
    │   ├── 0000000000000006-00000000002feadb.snap
    │   ├── 0000000000000006-00000000003011ec.snap
    │   └── db
    └── wal
        ├── 000000000000001d-00000000002a3805.wal
        ├── 000000000000001e-00000000002ba99f.wal
        ├── 000000000000001f-00000000002d1b40.wal
        ├── 0000000000000020-00000000002e7f47.wal
        ├── 0000000000000021-00000000002fe976.wal
        └── 0.tmp

3 directories, 12 files
```
###### 創建資料
我們使用[[K8s - Secrets]] 的 `my-sql` 當作實踐資料
###### 創建 etcd 快照：
```shell
tai@tai-virtual-machine:~$ sudo ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  snapshot save /var/lib/dat-backup.db
{"level":"info","ts":"2024-07-28T15:25:25.239+0800","caller":"snapshot/v3_snapshot.go:65","msg":"created temporary db file","path":"/var/lib/dat-backup.db.part"}
{"level":"info","ts":"2024-07-28T15:25:25.247+0800","logger":"client","caller":"v3/maintenance.go:211","msg":"opened snapshot stream; downloading"}
{"level":"info","ts":"2024-07-28T15:25:25.247+0800","caller":"snapshot/v3_snapshot.go:73","msg":"fetching snapshot","endpoint":"https://127.0.0.1:2379"}
{"level":"info","ts":"2024-07-28T15:25:25.299+0800","logger":"client","caller":"v3/maintenance.go:219","msg":"completed snapshot read; closing"}
{"level":"info","ts":"2024-07-28T15:25:25.305+0800","caller":"snapshot/v3_snapshot.go:88","msg":"fetched snapshot","endpoint":"https://127.0.0.1:2379","size":"3.3 MB","took":"now"}
{"level":"info","ts":"2024-07-28T15:25:25.305+0800","caller":"snapshot/v3_snapshot.go:97","msg":"saved","path":"/var/lib/dat-backup.db"}
Snapshot saved at /var/lib/dat-backup.db
```
###### 驗證快照：
```shell
tai@tai-virtual-machine:~$ sudo ls /var/lib/
AccountsService  cni                  fprint       logrotate       power-profiles-daemon  systemd                  upower
acpi-support     colord               fwupd        man-db          private                tpm                      usb_modeswitch
alsa             command-not-found    gdm3         misc            python                 ubiquity                 usbutils
app-info         containerd           geoclue      NetworkManager  saned                  ubuntu-advantage         vim
apport           dat-backup.db        ghostscript  nfs             sgml-base              ubuntu-drivers-common    vmware
apt              dbus                 grub         openvpn         shells.state           ubuntu-release-upgrader  whoopsie
aspell           dhcp                 hp           os-prober       shim-signed            ucf                      xfonts
avahi-autoipd    dictionaries-common  ispell       PackageKit      snapd                  udisks2                  xkb
bluetooth        dpkg                 kubelet      pam             snmp                   unattended-upgrades      xml-core
boltd            emacsen-common       libreoffice  plymouth        sudo                   update-manager
BrlAPI           etcd                 locales      polkit-1        swcatalog              update-notifier
tai@tai-virtual-machine:~$ sudo ETCDCTL_API=3 etcdctl --write-out=table \
  snapshot status /var/lib/dat-backup.db
Deprecated: Use `etcdutl snapshot status` instead.

+----------+----------+------------+------------+
|   HASH   | REVISION | TOTAL KEYS | TOTAL SIZE |
+----------+----------+------------+------------+
| 28158da8 |  2770309 |       1057 |     3.3 MB |
+----------+----------+------------+------------+
tai@tai-virtual-machine:~$
```

