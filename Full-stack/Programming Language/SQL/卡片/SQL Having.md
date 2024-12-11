---
Date: 2024-02-19
tags:
  - SQL
relation:
---
# 描述
HAVING 子句是用來取代 [WHERE](https://www.fooish.com/sql/where.html) 搭配聚合函數 (aggregate function) 進行條件查詢，因為 WHERE 不能與聚合函數一起使用。

聚合函數指的也就是 AVG()、COUNT()、MAX()、MIN()、SUM() 等這些內建函數。

**一句話概括:**
對分組結果進行條件過濾，確保只有滿足特定條件的分組被包含在查詢的結果中。
# 語法
```sql
SELECT column_name(s), aggregate_function(column_name) 
FROM table_name
WHERE column_name operator value 
GROUP BY column_name1, column_name2... 
HAVING aggregate_function(column_name) operator value;
```
# 實例
假設我們想從下面的 orders 資料表中查詢訂單金額總合小於 1000 的顧客：

![[SQL Having 1.png]]

我們可以下這個 SELECT 查詢敘述句：

```sql
SELECT Customer, SUM(Price) 
FROM orders 
GROUP BY Customer 
HAVING SUM(Price)<1000;
```

*Ans*

![[SQL Having 2.png]]