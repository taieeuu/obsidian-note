---
Date: 2024-03-24
tags:
  - Python
  - 方法
---
# 描述
如果有些列是數字，有些列是字母，或有些是bool型，那麼如果我們有時候只需要選取特定資料類型的列，那麼我們可以使用 DataFrame.select_dtypes(include[int], exclude=None)
# 語法
```python
df.select_dtypes(include=['float64', 'int64'])
```
# 實例
```python
df.select_dtypes(include=['float64', 'int64'])
```