---
Date: 2024-11-09
---
## 概述
Flyte Admin 是 Flyte 平台的核心組件之一,主要負責:

- 提供 REST API 接口,用於與 Flyte 平台交互

- 管理 Flyte 的元數據存儲

- 調度 workflow 執行

- 提供 Flyte 控制台 UI

## REST API
Flyte Admin 提供了豐富的 REST API,用於操作 Flyte 實體,例如:

- 創建、讀取、更新和刪除 workflows、tasks、launchplans 等

- 觸發 workflow 執行

- 查詢 workflow 執行狀態和結果

這些 API 使得與 Flyte 平台的交互變得簡單和標準化。
## 元數據管理
Flyte Admin 負責將 workflow、task 等定義持久化到數據庫中,作為 Flyte 的元數據存儲。這樣即使 Flyte Admin 重啟,也能保留所有的 Flyte 實體定義。
## Workflow 調度
當用戶觸發 workflow 執行時,Flyte Admin 會負責調度執行。它與 FlytePlugins 和 FlytePropeller 組件協作,將 workflow 調度到 K8s 集群上運行。整個過程對用戶是透明的。
## 控制台 UI
Flyte Admin 還提供了一個控制台 UI,用於可視化管理 Flyte 實體和監控 workflow 執行。用戶可以通過 UI 界面查看 workflow 定義、觸發執行、查看執行狀態和日誌等。
## 在 CI 中的作用
在 Flyte 項目的 GitHub Actions CI 工作流中,flyteadmin 的相關任務包括:

- 構建 flyteadmin 的 Docker 鏡像,用於集成測試

- 運行 flyteadmin 的集成測試,確保功能正確性

- 檢查 flyteadmin 的代碼生成是否最新

- 構建 flyteadmin 的 Docker 鏡像,作為 Flyte 平台的一部分

通過這些 CI 任務,確保 flyteadmin 組件的代碼質量和功能正確性,並構建可部署的鏡像。
## 總結
Flyte Admin 作為 Flyte 平台的核心組件,提供了 API 接口、元數據管理、workflow 調度以及控制台 UI 等關鍵功能,是 Flyte 平台不可或缺的一部分。