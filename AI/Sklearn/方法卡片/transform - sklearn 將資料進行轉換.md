---
Date: 2024-03-24
tags:
  - Sklearn
  - machinea_learning
  - 方法
---
# 描述
將資料進行轉換，比如資料的歸一化和標準化，將測試資料按照訓練資料同樣的模型進行轉換，得到特徵向量。
# 語法
```python
transform(X)
```
# 實例
```python
data_test_norm_transform = minmaxscaler.transform(data_test)
data_test_norm_transform = pd.DataFrame(data_test_norm_transform, columns=new_column_name)
```
