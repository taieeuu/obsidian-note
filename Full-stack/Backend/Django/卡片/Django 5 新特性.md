---
Date: 2024-08-11
tags:
  - Python
  - Django
---
目前 Django 最新版本為 5.x，他只支持 `python 3.10` 或以上版本。新版本的模型在模型、Admin後台系統、表單、異步身分驗證等進行改進。

(1) 新增了模型字段`GeneratedField`，主要用於模型字段計算處理
* `expression`: 用於設置字段計算規則
* `output_field`: 用於設置字段數據類型
* `db_persist`: 表示字段是否占用存儲空間、若db_persist 為 True；模型字段在數據表中生成相應字段 ，反之。

(2) 新增模型字段屬性 `db_default`，用於設置表字段的默認值。他與字段屬性 `default` 相似，但 `db_default` 是數據表的默認值，而 `default` 是模型字段的默認值。

(3) 優化模型字段屬性 `choices`，支持二級選項。

(4) 新增 `Admin 後台系統` 配置屬性 `show_facets` ，其屬性值分別為 `admin.ShowFacets.ALWAYS`、`admin.ShowFacets.ALLOW`、`admin.ShowFacets.NEVER`, 主要用於控制過濾器的數量。

(5) 優化了表單字段渲染，引入**字段組** 和字段組模板的概念，減少渲染表單所需的 HTML 和模板代碼量，使模板更簡潔更異於維護。