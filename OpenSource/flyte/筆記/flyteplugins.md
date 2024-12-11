---
Date: 2024-11-09
---
## 目錄結構
- go/tasks/: 包含各種Flyte插件的Go實現代碼
- tests/: 端到端的測試
## 使用地方
- Flytekit的Python SDK中,讓用戶可以定義各種類型的任務(task),如Spark、Athena等。這部分在flytekit的plugins目錄下。

- FlytePropeller中,作為一個庫被加載。FlytePropeller啟動時會加載flyteplugins,使其知曉有哪些插件以及它們與任務執行的依賴關係。但FlytePropeller並不關心這些插件具體是如何執行的。

- 在定義各種後端插件時,如K8s operator插件(Spark等)、Web API插件(Athena、BigQuery、Databricks、Snowflake等)以及Core插件。這些插件的實現都在flyteplugins項目中。

- 在FlytePropeller的plugins/loader.go中導入各個插件,使得FlytePropeller啟動時可以加載它們。

- 在flyteplugins/tests下有端到端測試,用於測試插件的整個生命週期。

- 在一些設計文檔如rfc/system/0000-exteranl-plugin-service.md中,描述了基於flyteplugins構建一個插件註冊中心服務的設想,使得用戶和FlytePropeller都可以向其發請求來運行各種類型的作業。

總之,flyteplugins是Flyte插件化架構的核心所在,為Flyte提供了極大的擴展性,使其可以通過插件的形式與各種不同的數據處理、機器學習平台進行整合,滿足多樣化的任務編排需求。它在Flyte的整個生態中都發揮著重要作用。