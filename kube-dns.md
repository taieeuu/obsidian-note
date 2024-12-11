---
Date: 2024-07-24
---
### kube-dns 是什麼？
kube-dns 是 Kubernetes 中的 DNS 解決方案，負責為集群中的服務和 Pod 提供 DNS 名稱解析。它確保集群內部所有服務和 Pod 可以通過 DNS 名稱相互發現並通信。
### kube-dns 的工作原理
1. **服務名稱解析**：當 Kubernetes 集群中的一個服務創建時，kube-dns 會自動為這個服務生成一個 DNS 條目。
2. **DNS 區域**：kube-dns 為每個命名空間創建一個獨立的 DNS 區域。例如，`default` 命名空間中的服務將有一個區域 `default.svc.cluster.local`。
3. **DNS 條目結構**：
    - 對於每個服務，kube-dns 生成一個完全限定域名 (FQDN)。
    - 例如，名為 `redis` 的服務在 `default` 命名空間中的 FQDN 是 `redis.default.svc.cluster.local`。