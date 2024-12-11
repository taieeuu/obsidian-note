---
Date: 2024-03-23
tags:
  - machinea_learning
  - 方法
  - Sklearn
relation:
---
# 描述
在機器學習中，常需要做數據的轉換，比如像`SimpleImputer`类可用于替换缺少的值，`MinMaxScaler`类可用于缩放数值，而`OneHotEncoder`可用于编码分類變量。

ColumnTransformer()，可以選擇地進行資料轉換。 例如，它允許將特定的轉換或轉換序列僅應用於數字列，而將單獨的轉換序列僅應用於類別列。
# 語法
用ColumnTransformer，必須指定一個轉換器清單。 每個轉換器是一個三元素元組，用於定義轉換器的名稱，要應用的轉換以及要應用於其的列索引，例如：`（名稱，對象，列）`
# 實例
1、ColumnTransformer將OneHotEncoder應用於列的0和1。

```python
transformer = ColumnTransformer(transformers=[('cat', OneHotEncoder(), [0, 1])])
```

2、對數字列0和1應用具有中值插補的SimpleImputer，對分類列2和3應用最頻繁插補的SimpleImputer。
```python
t = [('num', SimpleImputer(strategy='median'), [0, 1]), ('cat', SimpleImputer(strategy='most_frequent'), [2, 3])]
transformer = ColumnTransformer(transformers=t)
```

3、如果第0列和第1列是數字列，而第2列和第3列是分類列，而我們只想轉換分類資料並不會改變數字列，則可以以下方式定義
```python
transformer = ColumnTransformer(transformers=[('cat', OneHotEncoder(), [2, 3])], remainder='passthrough')
```