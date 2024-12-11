---
Date: 2024-03-30
tags:
  - Python
  - 視覺化
  - machinea_learning
---
實作Link : https://www.kaggle.com/code/chungmintai/practice-online-food-dataset/edit 
# 安裝
## install
```python
import matplotlib.pyplot as plt
import seaborn as sns
```

```python
fig, ax = plt.subplots(2,2, figsize=(12, 8))
```

# 設置
## 標頭
```python
ax[0,0].settitle('title')
```
## 調整座標字型樣式
```python
ax[0,0].tick_params(axis=調整的軸)
```
> 其他參數 : 距離, 長度, 顏色, 字體大小, 字形旋轉等
## 自動調整圖形佈局
以確保圖形中的所有元素都適當地顯示，避免它們之間的重疊。 此函數會自動調整子圖之間的間距、座標軸標籤的位置等，以使圖形更具可讀性。
```python
plt.tight_layout()
```
## 設定圖例的標題
```python
plt.legend(title='名稱')
```
> 參數 :
> bbox_to_anchor : 指定圖例相對於圖形的位置
> loc : 指定圖例的對齊方式
# 圖表類型
## 計數直方圖
```python
sns.countplot(x=數據欄位, data=數據, ax=位置)
```
> 參數 : 
> - hue : 對 x 數據進行分組，且進行不同的著色
## 散點圖
```python
sns.scatterplot(x=數據欄位, y=數據欄位, data=數據, ax=位置)
```
> 參數 : 
> - hue : 指定了根據`選取hue中的欄位`對散點進行著色。 這意味著散點圖中不同欄位的數據點將有不同的顏色。
> - style : 指定了根據`選取欄位`對散點進行不同的標記樣式。 這意味著對已經選取的x, y再針對不同欄位的數據點將有不同的標記樣式。
> - s : 樣式大小
## 箱線圖
```python
sns.boxplot(x=數據欄位, y=數據欄位, hue='Output', data=數據)
```
## 熱圖
1. 首先要選取數據中的欄位，且計算各列之間的相關係數矩陣
```python
correlation_matrix = data[['Age', 'Family size', 'latitude', 'longitude', 'Pin code']].corr()
sns.heatmapI(correlation_matrix)
```
> 參數 :
> - annot : 熱力圖上顯示數值標籤
> - cmap : 設置了顏色地圖為冷暖色調
> - linewidths : 設定了格子之間的線寬度