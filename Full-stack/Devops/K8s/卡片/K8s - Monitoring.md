---
Date: 2024-07-31
---
---
在 Kubernetes (K8s) 環境中，常用來監控 Pod 的工具確實包括 Prometheus、Grafana 和 ELK Stack，此外還有其他幾個業界常用的監控工具。以下是詳細的補充：

1. **Prometheus**
    - **功能**：系統監控和警報工具，提供強大的資料收集和查詢功能。
    - **特點**：支援多種資料來源、強大的查詢語言 PromQL、豐富的社群和插件支援。
    - **使用案例**：監控 Kubernetes 集群的資源使用情況（如 CPU、記憶體、網路流量等）。
2. **Grafana**
    - **功能**：資料視覺化工具，通常與 Prometheus 搭配使用。
    - **特點**：提供美觀的儀表板、多種資料來源整合、自訂圖表和警報功能。
    - **使用案例**：視覺化 Kubernetes 集群的監控資料，建立實時監控儀表板。
3. **ELK Stack (Elasticsearch, Logstash, Kibana)**
    - **功能**：日誌資料收集、分析和視覺化。
    - **特點**：Elasticsearch 提供強大的搜尋和分析功能，Logstash 用於資料收集和處理，Kibana 用於資料視覺化。
    - **使用案例**：集中化管理和分析 Kubernetes 集群中的應用日誌和系統日誌。
4. **Jaeger**
    - **功能**：分散式追蹤系統，用於監控和故障排除微服務架構中的交易流程。
    - **特點**：支援 OpenTracing 標準、提供詳細的追蹤資訊。
    - **使用案例**：追蹤 Kubernetes 中各服務之間的請求流程，找出瓶頸和故障點。