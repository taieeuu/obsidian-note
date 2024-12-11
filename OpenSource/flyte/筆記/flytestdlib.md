---
Date: 2024-11-09
---
## Readme
### Common Go Tools
#### 簡介
- 介紹 flytestdlib 庫，包含各種常用的 Go 組件
#### 組件
- config: 支持強類型配置，可以將配置表示為 Go 結構體

- cli/pflags: 可以為給定的結構體生成 pflags

- storage: 抽象存儲庫，使用 stow 連接 s3/azure/gcs，支持配置工廠、內存存儲(用於測試)和原生 protobuf

- contextutils: 圍繞 Go 的 context 進行封裝，用於設置/獲取已知鍵

- logger: 可配置、可標記、上下文感知的 logrus 封裝

- profutils: 啟動 HTTP 服務器，提供 /metrics、/healthcheck 和 /version 端點

- promutils: 提供更方便的方式來構建 Prometheus 指標並按組件劃分範圍

- atomic: 對 sync.atomic 庫進行封裝，提供 AtomicInt32 等方便的類型

- sets: 提供強類型且方便的接口集

- utils

- version
### 貢獻指南
- 版本控制: 遵循語義化版本控制

- 發布流程: 使用 goreleaser，維護者通過創建標籤來發布新版本
## 目錄結構
### atomic 目錄
提供原子操作的函式庫。

- error.go: 定義了一些錯誤處理相關的函式和型別

- int32.go: 提供對 int32 型別的原子操作

- string.go: 提供對字串的原子操作
### cli 目錄
提供命令列介面相關的函式庫。

- pflags: 封裝了 pflag 套件,定義了一些常用的 flag 型別
### config 目錄
提供設定管理相關的函式庫。

- config.go: 定義了 Config 介面和相關函式,用於載入和管理設定

- runtime: 提供執行時期的設定管理

- storage: 提供儲存相關的設定管理

- tests: 設定相關的測試程式碼
### contextutils 目錄
提供 context 相關的 utility 函式。
### logger 目錄
提供日誌記錄相關的函式庫。

- glog: 基於 glog 套件實作的日誌記錄器

- mocks: 日誌記錄器的 mock 實作,用於測試

- utils.go: 日誌記錄相關的 utility 函式
### profutils 目錄
提供效能分析相關的函式庫。
### promutils 目錄
提供 Prometheus 相關的函式庫,用於匯出 metrics。

- labeled: 帶有標籤的 metrics

- scope: 定義了 metrics 的作用域

- utils.go: Prometheus 相關的 utility 函式
### storage 目錄
提供儲存相關的函式庫。

- stow: 基於 stow 套件實作的儲存介面
### tests 目錄
提供測試相關的函式庫。

- cmp: 提供比較物件的函式

- golden: 提供 golden file 測試的函式

- random: 提供隨機資料產生的函式

- storage: 提供測試儲存功能的函式
### utils 目錄
提供各種 utility 函式。

- errs.go: 錯誤處理相關的函式

- maps.go: Map 相關的函式

- sets.go: Set 相關的函式

- strings.go: 字串處理相關的函式

以上是我整理的 flytestdlib 專案筆記,這個專案提供了許多 Flyte 專案共用的函式庫,包括原子操作、命令列介面、設定管理、日誌記錄、效能分析、Prometheus 支援、儲存介面、測試工具等等,是 Flyte 專案的基礎建設之一。

