---
Date: 2024-11-09
---
- Flyte Propeller 是一個在 Kubernetes 上原生執行 Flyte 工作流的 operator

- 使用 Protobuf 編寫工作流規範,實現跨語言兼容性
## 組件
### 1. Propeller
- 一個 K8s 原生 operator,用於執行 Flyte 工作流
### 2. Propeller Webhook
- 一個可選部署的 Mutating Webhook

- 擴展 Flyte Propeller 的功能

- 支持通過 Flyte 後端插件將 secrets 注入到直接或間接啟動的 pod 中
### 3. kubectl-flyte
- 一個與 Flyte Workflow CRD 交互的 kubectl 插件

- 支持在命令行中檢索和渲染 Flyte 工作流

- 支持安全地中止正在運行的工作流
## 入門指南
### 1. 安裝 kubectl-flyte 工具
- 使用 make compile 命令安裝到 ~/go/bin 目錄

- 或使用 Krew 安裝: `kubectl krew install flyte`
### 2. 使用 kubectl-flyte
- 單獨使用: `kubectl-flyte --help`

- 作為 kubectl 子命令: `kubectl flyte --help`
### 3. 觀察運行中的工作流
- 檢索命名空間中所有工作流: `kubectl-flyte get --namespace <namespace>`

- 檢索特定工作流: `kubectl-flyte get <namespace>/<name>`
### 4. 刪除工作流
- 刪除特定工作流: `kubectl-flyte delete --namespace <namespace> <name>`

- 刪除所有已完成工作流: `kubectl-flyte delete --namespace <namespace> --all-completed`
### 5. 在本地運行 propeller
- 使用項目根目錄中的 config.yaml 文件

- 運行 flytepropeller 命令

- 需要 Blob 存儲和 Admin Service 端點等依賴
## 目錄結構
### cmd 目錄
主要存放命令列工具相關的程式碼。

- kubectl-flyte/ : kubectl-flyte 命令列工具的實作

- webhook/ : Webhook 相關的命令
### events 目錄
存放事件處理相關的程式碼。
### hack 目錄
存放開發相關的腳本和工具。

- boilerplate/ : 版權宣告範本

- update-codegen.sh : 更新自動生成程式碼的腳本
### manager 目錄
存放 manager 相關的程式碼,manager 負責管理和調度 workflow 的執行。
### pkg 目錄
專案的主要套件程式碼。

- apis/ : 定義 CRD (Custom Resource Definition) 的程式碼

- client/ : 用於存取 Kubernetes API 的 client

- compiler/ : workflow 編譯器相關程式碼

- controller/ : workflow controller 實作

- utils/ : 共用的 utility 函式

### plugins 目錄
存放 Flyte 的插件實作。

- awsbatch/ : AWS Batch 插件

- k8s/ : Kubernetes 插件

- kfoperators/ : KubeFlow 插件

- pod/ : Pod 插件

- spark/ : Spark 插件
### raw_examples 目錄
存放 workflow 的 yaml 範例。
### script 目錄
存放部署和操作相關的腳本。
### version 目錄
定義專案的版本資訊。

以上是我根據提供的目錄結構,整理出的 Flyte Propeller 專案筆記。這個專案的核心是 pkg 目錄下的套件,實作了 workflow 的定義、編譯、管理和執行等功能。plugins 目錄則提供了各種執行環境的插件支援。cmd 目錄實作了命令列工具。專案的腳本和開發工具放在 hack 和 script 目錄下。