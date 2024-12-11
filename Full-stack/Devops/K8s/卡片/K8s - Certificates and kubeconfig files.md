---
Date: 2024-07-27
---
---
### Certificates 和 PKI
使用 `kubeadm` 建立的集群會進行以下操作：

1. **建立自簽名的證書授權機構 (Certificate Authority, CA)**：
    - CA 的相關文件儲存於 `/etc/kubernetes/pki`。
    - `ca.key`：私鑰。
    - `ca.crt`：CA 證書，會複製到各個集群節點上，使 Node 信任由這個 CA 簽名的證書。（同時也會在 kubeconfig 文件裡）
2. **為系統組件生成證書**：
    - `kubernetes-admin` 用戶被建立。
3. **創建 `kubernetes-admin` 用戶**：
	- 此用戶擁有管理集群的權限，其證書信息會包含在 `admin.conf` 中，用於 kubeconfig 文件。
