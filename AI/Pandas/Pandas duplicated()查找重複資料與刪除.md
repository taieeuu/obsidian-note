---
Date: 2024-02-25
tags:
  - Python
  - 方法
---
# 描述
資料預處理使用
# 語法
Pandas套件的duplicated()方法(Method)除了能夠查找所有欄位資料完全一樣的重複資料外，也提供了以下兩個關鍵字參數，來客製化查找的方式：

- **subset**查找特定欄位的重複資料
- **keep**保留第一筆(first)、最後一筆(last)或全部(False)的重複資料
```python
import pandas as pd

df = pd.read_csv('bestsellers with categories.csv')
column_names = ['Name', 'Author', 'Year', 'Genre']
df = df[df.duplicated(subset=column_names, keep=False)]

print(df)
```
![[Pasted image 20240225193358.png]]
# 實例
## 檢查重複
```python=
import pandas as pd

df = pd.read_csv('bestsellers with categories.csv')
print(df)
```
*輸出*
![[Pasted image 20240225192941.png]]
## 使用後
```python
import pandas as pd

df = pd.read_csv('bestsellers with categories.csv')
print(df.duplicated())
```
![[Pasted image 20240225193414.png|196]]
# 刪除重複則可使用drop_duplicates()

```python
import pandas as pd

df = pd.read_csv('bestsellers with categories.csv')

df.drop_duplicates(inplace=True)
```

如果要刪除特定則可透過subset及keep關鍵字
```python
import pandas as pd

df = pd.read_csv('bestsellers with categories.csv')

column_names = ['Name', 'Author', 'Year', 'Genre']

df.drop_duplicates(subset=column_names, keep='first', inplace=True)
```