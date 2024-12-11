---
Date: 2024-03-18
tags:
  - SQL
relation:
---
# 描述
用來限制 SQL 查詢語句最多只影響幾筆資料
# 語法
```sql
LIMIT offset, count
```
* offset: 代表從第幾個row開始回傳  
* count: 總共要回傳幾個row
# 實例
此題為[[177. Nth Highest Salary 第N最高薪水🟠]]。我們直接看到`limit`該行，他針對輸入的m進行要從第幾個回傳，且回傳數為1。
```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT  
BEGIN  
#宣告變數  
DECLARE m INT;  
#指派值  
SET m = N-1;  
  RETURN (  
      # Write your MySQL query statement below.  
      SELECT Salary  
      FROM Employee  
      GROUP BY Salary  
      ORDER BY Salary DESC  
      LIMIT m,1  
  );  
END# LIMIT offset,count  
# offset: first row to return  
# count: 要回傳幾個row
```