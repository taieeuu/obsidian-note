---
Date: 2024-07-21
tags:
  - k8s
  - devops
---
在 Kubernetes 中，Persistent Volumes (PV) 和 Persistent Volume Claims (PVC) 是用來管理持久化存儲的抽象。以下是它們的概述和使用方法。
## Persistent Volumes (PV)
Persistent Volume (PV) 是集群中的一塊存儲資源，它由管理員進行配置。PV 與 Pod 是獨立的，PV 的生命週期不受 Pod 的影響。
##### PV 的主要屬性包括：
- **容量 (Capacity)**: PV 的存儲大小。
- **存儲類別 (Storage Class)**: 定義 PV 的存儲屬性，如性能和備份策略。
- **訪問模式 (Access Modes)**: 定義如何掛載 PV。常見的訪問模式有：
    - ReadWriteOnce (RWO)：單個節點讀寫。
    - ReadOnlyMany (ROX)：多個節點只讀。
    - ReadWriteMany (RWX)：多個節點讀寫。
- **回收策略 (Reclaim Policy)**: 定義 PV 釋放後的行為。常見的策略有：
    - Retain：保留數據，需手動清理。
    - Recycle：基本清理（刪除所有文件）。
    - Delete：刪除存儲資源。
## Persistent Volume Claims (PVC)
Persistent Volume Claim (PVC) 是用戶對 PV 的請求。Pod 通過 PVC 來使用 PV。PVC 指定了所需的存儲大小和訪問模式，Kubernetes 會自動找到合適的 PV 並進行綁定。
##### PVC 的主要屬性包括：
- **請求容量 (Requested Storage)**: 需要的存儲大小。
- **訪問模式 (Access Modes)**: 與 PV 的訪問模式一致。
## 靜態配置 (Static Provisioning) 工作流程
1. 創建 PersistentVolume (PV)
2. 創建 PersistentVolumeClaim (PVC)
3. 在 Pod 中使用 PVC
## Storage Lifecycle
Kubernetes 中的持久化存儲有一個完整的生命週期，包括綁定、使用和回收階段。
##### 1. 綁定 (Binding)
當 PersistentVolumeClaim (PVC) 被創建時，Kubernetes 會自動匹配一個合適的 PersistentVolume (PV) 並進行綁定。這個過程包括以下步驟：

- **PVC 創建**：用戶創建一個 PVC，請求特定大小和訪問模式的存儲。
- **PV 匹配**：Kubernetes 會搜索所有可用的 PV，尋找符合請求的 PV。
- **綁定**：找到匹配的 PV 後，Kubernetes 會將 PVC 和 PV 綁定在一起，這時 PV 的狀態會變為 `Bound`。
##### 2. 使用 (Using)
當 Pod 使用 PVC 時，這個過程會持續到 Pod 的生命週期結束。這個階段包括：

- **Pod 創建並掛載 PVC**：在 Pod 的規範中，定義使用 PVC 來掛載存儲卷。
- **存儲卷使用**：Pod 中的容器通過掛載點讀寫數據，使用 PV 提供的存儲資源。
- **Pod 終止**：當 Pod 被刪除或終止，PVC 和 PV 的狀態保持不變，PV 仍然處於 `Bound` 狀態，準備為下一個 Pod 使用。
##### 3. 回收 (Reclaim)
當 PVC 被刪除時，PV 的回收策略 (Reclaim Policy) 決定了 PV 和其數據的處理方式。主要有兩種回收策略：

- **Delete**：當 PVC 被刪除時，PV 和其數據也會被刪除。這意味著 PV 被完全清理，並且存儲空間會等待下一次的 PVC 使用。
    - PV 的數據會被永久刪除。
    - PV 的狀態會變為 `Available`，準備為下一個 PVC 重新使用。
- **Retain**：當 PVC 被刪除時，PV 會保留數據，但狀態會變為 `Released`。這意味著 PV 不能馬上被下一個 PVC 使用，需要管理員手動清理或回收。
    - PV 的數據會被保留。
    - PV 的狀態會變為 `Released`，管理員需要手動刪除或清理數據。
    - 管理員可以手動處理數據後，重新將 PV 設置為 `Available` 狀態，準備為下一個 PVC 重新使用。