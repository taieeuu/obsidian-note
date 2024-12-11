---
Date: 2024-03-31
tags:
  - Sklearn
  - machinea_learning
---
#  數據處理
## 1. 分別透過呼叫 fit_transform() 和 transform()處理測試集
將訓練集歸一化，這裡採用Min-Max歸一化

```python
data_test_norm_transform = minmaxscaler.transform(data_test)
data_test_norm_transform = pd.DataFrame(data_test_norm_transform, columns=new_column_name)
data_test_norm_fit_transform = minmaxscaler.fit_transform(data_test)
data_test_norm_fit_transform = pd.DataFrame(data_test_norm_fit_transform, columns=new_column_name)
```
### 1.1  fit_transform()的结果：
![[sklearn - transform()和fit_transform()深入理解 1.png]]
### 1.2   transform()的结果
![[sklearn - transform()和fit_transform()深入理解 2.png]]
# 猜测
## 2. 驗證猜想二
不用sklearn的方法，手動編程。 在歸一化測試集時，使用測試集自己的最小值和最大值
```python
((data_test - data_test.min()) / (data_test.max()-data_test.min())).head(3)
```
![[sklearn - transform()和fit_transform()深入理解 3.png]]
比較2.1節的結果，可以看到兩者是相同的。

所以猜想二正確。
## 2.1 验证猜想一 
在归一化测试集时，使用训练集的最小值和最大值
```python
((data_test - data_train.min()) / (data_train.max()-data_train.min())).head(3)
```
![[sklearn - transform()和fit_transform()深入理解 4.png]]
对比2.2节的结果，发现手动编程的结果与用sklearn中transform()的结果是相同的。

所以猜想一正确。
# 總結
在用機器學習解決問題時，會將資料集分成訓練集和測試集。 我們可以先用fit_transform()方法處理訓練集，再用transform()方法處理測試集。 這時，在歸一化測試集時，使用的是訓練集的統計量，這麼做是為了讓訓練集和測試集更相似。 使演算法在兩者上的表現盡可能相同

而若對測試集使用了fit_transform()方法，則會用測試集自己的統計量來歸一化資料。

在測試集上千萬不要混用這兩個方法，筆者就因為在測試集上使用了fit_transform()方法，導致測試集上的損失一直比驗證集上的大很多！

還有一個fit()方法沒說，這個是最簡單的，它和fit_transform()是相同的，只不過後者會回傳轉換後的結果，而前者是不會回傳的，只會訓練轉換器。