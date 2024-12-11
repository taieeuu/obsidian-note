---
Date: 2024-08-17
---
Django 的 `Generic Editing Views` 是一組通用視圖類別，用於處理對象的創建、更新和刪除操作。`Generic Editing Views` 主要包含 `CreateView` 、 `UpdateView`、`DeleteView`、`FormView`。
### CreateView
`CreateView` 是用於處理對象創建的通用視圖。它通常用於顯示和處理一個表單，當表單被提交且數據驗證通過後，`CreateView` 會自動將新對象保存到數據庫中。
#### 工作流程：
- 顯示一個空的表單（GET 請求）。
- 處理表單提交（POST 請求），驗證數據並創建新對象。
### UpdateView
`UpdateView` 是用於處理對象更新的通用視圖。它通常用於顯示和處理一個表單，當表單提交並通過驗證後，`UpdateView` 會自動更新對象的字段。
#### 工作流程：
- 顯示一個包含當前對象數據的表單（GET 請求）。
- 處理表單提交（POST/PATCH 請求），驗證數據並更新對象。
### DeleteView
`DeleteView` 是用於處理對象刪除的通用視圖。當接收到 DELETE 請求時，`DeleteView` 會刪除指定的對象，並在成功後重定向到指定的 URL。
#### 工作流程：
- 顯示一個確認刪除的頁面（GET 請求）。
- 處理刪除請求（POST/DELETE 請求），刪除對象並重定向。
### FormView
`FormView` 是用於處理一般表單提交的通用視圖，不直接與特定模型相關聯。它非常靈活，適用於處理任何表單提交邏輯，比如搜索、聯繫表單等，實現網頁上的數據交互，用戶在網頁上輸入訊息，然後提交到網站服務器端進行處理。
#### 工作流程：
- 顯示一個表單（GET 請求）。
- 處理表單提交（POST 請求），並在數據有效時執行自定義邏輯。