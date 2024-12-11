---
Date: 2024-03-23
tags:
  - machinea_learning
  - "#方法"
  - "#Sklearn"
---
# 描述
當數據有缺失值時，可以使用中間值、均值等去做填充該缺失值。
# 語法
* missing_values: 設定數據集中什麼代表缺失值，一般情況下DataFrame 中的空值(pd.NA)也會轉换成np.nan，所以通常都是填人np.nan(空值)
* 1. strategy: 設定策略，也就是如何去填補這些缺失值，有四個選項，為mean、median 、most_frequent和constant。
# 實例
```python
from sklearn.impute import SimpleImputer
import numpy as np

## 構建數據集
x = [[1, 2, np.nan], [28, np.nan, 36], [np.nan, 58, 66]]
print('Datasets: ', x)

## 設定SimpleImputer
## 缺失值以均值填充
imp_mean = SimpleImputer(missing_values = np.nan, strategy = 'mean')
```