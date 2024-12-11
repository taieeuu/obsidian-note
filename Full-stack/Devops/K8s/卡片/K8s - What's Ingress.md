---
Date: 2024-07-27
---
>[!info]
>[https://kubernetes.io/docs/concepts/services-networking/ingress/](https://kubernetes.io/docs/concepts/services-networking/ingress/)

---
Ingress 是 Kubernetes 中的一種 API 對象，它管理集群內服務（Services）對外部訪問的路由規則。簡單來說，Ingress 是一個應用層負載均衡器，允許外部 HTTP 和 HTTPS 訪問到 Kubernetes 集群內部服務。

Ingress 通過定義應用層的路由規則，使得可以根據請求的 URL 路徑和主機名來分配流量。這樣的應用層負載均衡不僅能夠有效地分配流量，還能提供更多的靈活性和控制，確保不同的請求被正確地路由到合適的服務。

簡單來說，如圖下的說法，與一般服務比較會清楚得很多。

![[K8s - What's Ingress 1.png]]

上述圖看懂之後，我們將更詳細進去 `Ingress` 去查看。

![[K8s - What's Ingress 2.png]]

簡單說，就是接收請求，並依照一定的路由規則，把請求轉送到對應的Service上去。
##### 兩個要求：
- Ingress Resource，就是一系列的路由規則 routing rules
-  Ingress Controller, 控制實作這些路由規則。([https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/))
##### Ingress 的一些主要特點：
1. **路由規則**：Ingress 允許你定義基於域名和路徑的路由規則。這些規則決定了 HTTP/HTTPS 請求應該轉發到哪個服務。
2. **負載均衡**：Ingress 提供內建的負載均衡功能，可以根據定義的路由規則，將流量分配到不同的服務實例上。
3. **SSL/TLS**：Ingress 支持 SSL/TLS 終止，這意味著你可以在 Ingress 層處理 HTTPS 流量，並將解密後的流量轉發到集群內的服務。
4. **虛擬主機**：Ingress 支持虛擬主機（Virtual Hosts），這使得你可以在同一個 Ingress 資源中為不同的域名配置不同的路由規則。
5. **外部訪問**：與 Kubernetes 中的其他資源（如 Service）不同，Ingress 允許你使用外部 IP 或 DNS 名稱來訪問集群內的服務。