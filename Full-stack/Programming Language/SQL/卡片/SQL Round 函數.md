---
Date: 2024-02-19
tags:
  - SQL
relation:
---
# 描述
decimals 用來設定要四捨五入到小數點第幾位，0 表示個位數。
# 語法
```sql
SELECT ROUND(column_name, decimals) 
FROM table_name;
```
# 實例
```sql
SELECT ROUND(Price, 0) FROM orders;

# --result--
|ROUND(Price, 0)|
|---|
|1000|
|2000|
|501|
```