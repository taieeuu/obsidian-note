---
Date: 2024-02-25
tags:
  - Python
  - 方法
---
# 描述
dropna（）函数用于删除具有Null / NaN值的行和列
# 語法
dropna([axis](https://so.csdn.net/so/search?q=axis&spm=1001.2101.3001.7020)=0, how='any', thresh=None, subset=None, inplace=False)

參數：
- axis：軸。 0或'index'，表示按行刪除；1或'columns'，表示按列刪除。
- how：篩選方式。 ‘any’，表示該行/列只要有一個以上的空值，就刪除該行/列；‘all’，表示該行/列全部都為空值，就刪除該行/列。
- thresh：非空元素最低數量。 int型，預設為None。 如果該行/列中，非空元素數量小於這個值，就刪除該行/列。
- subset：子集。 列表，元素為行或列的索引。 如果axis=0或‘index’，subset中元素為列的索引；如果axis=1或‘column’，subset中元素為行的索引。 由subset限制的子區域，是判斷是否刪除該行/列的條件判斷區域。
- inplace：是否原地替換。 布林值，預設為False。 如果為True，則在原始DataFrame上進行操作，傳回值為None。

# 實例
```python
# Check null & duplicated rows
print(df.isna().sum())
print(df.duplicated().sum())
```
![[從DataFrame中刪除Null and NA值 1.png]]
```python
df.dropna(inplace=True) #直接替換刪除完的資料
print(df.isna().sum())
```
![[從DataFrame中刪除Null and NA值.png]]