---
Date: 2024-07-28
---
---
etcd 是一個分散式鍵值存儲，用於 Kubernetes 集群中保存所有數據。它確保數據的一致性和高可用性，是 Kubernetes 集群的數據源。

etcd 作為靜態 Pod 運行，其配置文件位於 `/etc/kubernetes/manifests/etcd.yaml`。這些靜態 Pod 由 kubelet 直接管理，而不是由 Kubernetes API 服務器管理。當 kubelet 啟動時，它會檢查這個目錄中的文件並自動啟動或重啟這些 Pod。

**主要功能：**
1. **數據存儲：** 保存所有 Kubernetes 狀態數據，包括節點、Pod、服務和配置。
2. **一致性：** 使用 Raft 共識算法來確保數據的一致性。
3. **高可用性：** 支持集群模式來提高可用性，通常配置為多個節點。

**基本概念：**
- **鍵值存儲：** etcd 以鍵值對的形式存儲數據，類似於 NoSQL 資料庫。
- **節點 (Node)：** etcd 集群由多個節點組成，每個節點都是一個 etcd 實例。
- **領導者 (Leader)：** 在 Raft 共識算法中，有一個領導者負責處理所有的寫操作，其他節點為追隨者 (Follower)。

**etcd 的操作：**
- **寫入數據：** `etcdctl put <key> <value>`
- **讀取數據：** `etcdctl get <key>`
- **刪除數據：** `etcdctl del <key>`
- **列出所有鍵：** `etcdctl get "" --prefix --keys-only`

**etcdctl 是 etcd 的命令行工具，可以用來管理和操作 etcd 數據。**
**高可用性配置：**
- **集群配置：** 通常配置為奇數個節點（如 3、5、7）以避免分裂腦 (split-brain) 問題。
- **數據備份：** 定期備份 etcd 數據以防止數據丟失。
- **監控和告警：** 配置監控和告警系統來檢測 etcd 集群的狀態和性能。

**常見問題：**
1. **數據一致性問題：** 確保所有節點正常運行並同步數據。
2. **性能問題：** 優化 etcd 配置和資源分配以提高性能。
3. **故障恢復：** 確保有完善的備份和恢復計劃來應對故障。
### 安裝 etcdctl 工具
###### 確認 etcd 版本
```shell
tai@tai-virtual-machine:~$  kubectl exec -it --namespace kube-system etcd-tai-virtual-machine -- sh
sh-5.2# etcd --version
etcd Version: 3.5.10
Git SHA: 0223ca52b
Go Version: go1.20.10
Go OS/Arch: linux/amd64
sh-5.2# exit
exit
```
###### 從 GitHub 下載對應版本的 etcd
```shell
export RELEASE=3.5.3
wget https://github.com/etcd-io/etcd/releases/download/v${RELEASE}/etcd-v${RELEASE}-linux-amd64.tar.gz
tar -zxvf etcd-v${RELEASE}-linux-amd64.tar.gz
cd etcd-v${RELEASE}-linux-amd64
sudo cp etcdctl /usr/local/bin
```
###### 檢查版本
```shell
tai@tai-virtual-machine:~/etcd-v3.5.3-linux-amd64$ etcdctl version
etcdctl version: 3.5.3
API version: 3.5
```