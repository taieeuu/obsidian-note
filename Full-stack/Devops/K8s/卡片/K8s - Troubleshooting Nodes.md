---
Date: 2024-07-31
---
---
- Server online
- Network reachability
- systemd
- container runtime
- kubelet
- kube-proxy
### kubelet
```shell
# 取得狀態
systemctl status kubelet.service --no-pager

# 系統啟動時啟動
systemctl enable kubelet.service

# 啟動 kubelet
systemctl start kubelet.service

# 紀錄
sudo journalctl -u kubelet.service --no-pager

# systemd 服務單元 cfg
/etc/systemd/system/kubelet.service.d/10-kubeadm.conf

# kubelet config
/var/lib/kubelet/config.yaml
```