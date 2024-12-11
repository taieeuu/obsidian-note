---
Date: 2024-02-19
tags:
  - SQL
relation:
---
# 描述
AVG() 函數用來計算一數值欄位的平均值。
# 語法
```sql
SELECT AVG(column_name)
FROM table_name;
```
# 實例
假設我們想從下面的 students 資料表中查詢學生的平均身高：

|S_Id|Name|Height|
|---|---|---|
|1|張一|170|
|2|王二|176|
|3|李三|173|
```sql
SELECT AVG(Height) 
FROM students;
```

*輸出:*

| AVG(Height)| |
| --- | --- |
| 173 |  |
