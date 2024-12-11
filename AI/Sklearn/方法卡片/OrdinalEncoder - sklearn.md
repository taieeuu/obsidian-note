---
Date: 2024-03-23
tags:
  - "#方法"
  - machinea_learning
  - Sklearn
---
# 描述
將有序分類特徵轉換為整數標籤。有序分類特徵，例如教育程度（國中、高中、大學）或收入水平（低、中、高）。

**備註**
 僅能處理二維數據，即特徵矩陣的形狀為 `(n_samples, n_features)`。
# 語法
實例一個OrdinalEncoder()，且fit_tranform(data)
# 實例
```python
from sklearn.preprocessing import OrdinalEncoder

# 创建一个示例数据集
X = [['Male', 'High'],
     ['Female', 'Low'],
     ['Male', 'Medium']]

# 创建 OrdinalEncoder 对象
encoder = OrdinalEncoder()

# 使用 OrdinalEncoder 对数据进行拟合和转换
encoded_X = encoder.fit_transform(X)

print(encoded_X)

### output
[[1. 0.]
 [0. 1.]
 [1. 2.]]
```