---
Date: 2024-03-24
tags:
  - 方法
  - machinea_learning
  - Sklearn
---
# 描述
是由[[fit - sklearn 訓練]]以及[[transform - sklearn 將資料進行轉換]]所結合
# 語法
```python
data_test_norm_fit_transform = minmaxscaler.fit_transform(data_test)
data_test_norm_fit_transform = pd.DataFrame(data_test_norm_fit_transform, columns=new_column_name)
```
# 實例
```python
from sklearn.preprocessing import StandardScaler

# 假設您有一個特徵矩陣X
X = [[1, 2], [1, 4], [1, 0], [4, 2], [4, 4], [4, 0]]

# 創建StandardScaler物件
scaler = StandardScaler()

# 使用fit_transform()對特徵矩陣進行擬合和轉換
scaled_X = scaler.fit_transform(X)

# 打印轉換後的特徵矩陣
print(scaled_X)
```