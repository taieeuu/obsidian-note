---
Date: 2024-11-09
---
Flytectl 被設計為可與 Flyte 配合使用的便攜式輕量級命令列介面。它是用 Golang 編寫的，並存取 FlyteAdmin（Flyte 的控制平面）。
## 概述
Flytectl 是 Flyte 的官方命令行界面,主要功能包括:

- 與 FlyteAdmin 服務交互,支持 gRPC 和 REST 端點

- 提供各種命令,用於管理和操作 Flyte 實體,如 workflows、tasks、executions 等

- 支持多平台,包括 OSX、Linux 和 Windows

- 遵循 OAuth2 認證標準,與 FlyteAdmin 保持一致

Flytectl 旨在提供一種統一的命令行交互方式,最終取代 Flytekit 和 Flytecli。
## 為什麼要使用 Flytectl?
- 提供一致的命令行界面,簡化與 Flyte 平台的交互

- 支持多種輸出格式,如 YAML、JSON 和表格,方便處理和整合數據

- 提供強大的過濾、排序和選擇功能,便於管理大量的 Flyte 實體

- 跨平台支持,可在各種環境中使用
## 安裝
### OSX
使用 Homebrew 安裝:

```bash
brew install flyteorg/homebrew-tap/flytectl
```

升級現有安裝:

```bash
flytectl upgrade
```
### 其他操作系統
使用以下命令安裝:

```bash
curl -sL https://ctl.flyte.org/install | bash
```
## 使用示例
### 註冊工作流
```bash
# 註冊核心工作流
flytectl register examples -d development -p flytesnacks
```
### 獲取 workflows
```bash
flytectl get workflows [workflow-name] [-o yaml | -o json | default -o table] [--filters...] [--sort-by...] [--selectors...]
```
### 獲取 launch plans
```bash
flytectl get launch-plans [launchplan-name] [-o yaml | -o json | default -o table] [--filters...] [--sort-by...] [--selectors...]
```

獲取特定版本的 launch plan,並生成可用於創建 execution 的 YAML 模板:

```bash
flytectl get launch-plans launch-plan-name --execution-template -o YAML
```
## 貢獻指南
詳細的貢獻指南可以在 這裡 找到。
## 提交 Issue
如果你想提交 Issue,請參考貢獻指南中的 issues 部分。

Flytectl 作為 Flyte 的官方命令行界面,為用戶提供了一種統一、高效的方式來與 Flyte 平台交互。它支持多種命令和輸出格式,並提供強大的過濾和排序功能,使管理 Flyte 實體變得更加方便。Flytectl 是 Flyte 生態系統中不可或缺的一部分,為用戶提供了一種命令行驅動的開發和操作體驗。
