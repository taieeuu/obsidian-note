---
Date: 2024-02-19
tags:
  - SQL
relation:
---
# 描述

GROUP BY 语句可结合一些聚合函数来使用，是用來將查詢結果中特定欄位值相同的資料分為若干個群組，而每一個群組都會傳回一個資料列。若沒有使用 GROUP BY，聚合函數針對一個 SELECT 查詢，只會返回一個彙總值。 ps:有點像去重複值

聚合函數指的也就是 **AVG()、COUNT()、MAX()、MIN()、SUM()** 等這些內建函數。

**一句話概括:**
`GROUP BY`語句用於對結果集進行分組，這樣相同的值就會被合併成一個組。在這個過程中，它確實有點像是對特定欄位進行去除重複值的動作。
# 語法
```sql
SELECT column_name(s), aggregate_function(column_name) 
FROM table_name 
WHERE column_name operator value 
GROUP BY column_name1, column_name2...;
```
# 實例
假設我們想從下面的 orders 資料表中查詢個別顧客的訂單金額總合：

![[SQL GROUP BY 1.png]]

我們可以下這個 SELECT 查詢敘述句：

```sql
SELECT Customer, SUM(Price)
FROM orders 
GROUP BY Customer;
```

返回的結果如下：

![[SQL GROUP BY 2.png]]