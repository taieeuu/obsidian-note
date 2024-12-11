---
Date: 2024-02-19
tags:
  - SQL
relation:
---
# 描述
![[SQL Joins 1.png]]
在我们继续讲解实例之前，我们先列出您可以使用的不同的 SQL JOIN 类型：

- **INNER JOIN**：如果表中有至少一个匹配，则返回行
- **LEFT JOIN**：即使右表中没有匹配，也从左表返回所有的行
- **RIGHT JOIN**：即使左表中没有匹配，也从右表返回所有的行
- **FULL JOIN**：只要其中一个表中存在匹配，则返回行
# 語法
```sql
SELECT column1, column2, ...
FROM table1
JOIN table2 ON condition;
```
# 實例
Website 數據

![[SQL Join 2.png]]

Access_Log 數據

![[SQL Join 3.png]]

"Websites" 表中的 "id" 欄位指向 "access_log" 表中的欄位 "site_id"。 上面這兩個表是透過 "site_id" 欄位連結起來的。

然後，如果我們執行下面的 SQL 語句（包含 INNER JOIN）：

![[SQL Join 4.png]]