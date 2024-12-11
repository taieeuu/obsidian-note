---
Date: 2024-03-23
tags:
  - machinea_learning
  - 方法
  - Sklearn
---
# 描述
可以將數據預處理和建模流程封裝起來。在資料處理過程中，許多步驟都是重複或類似的，例如特徵選擇處理、歸一化、分類等等，pipeline就可以實現以下幾點好處：

* 簡化程式碼：資料預處理過程是很繁雜的，pipeline 可以直接將步驟封裝成完整的工作流程，避免了程式碼重複。
* 更少出Bug：流程規範化了，就能在避免在建模過程中漏掉某個步驟。
* 更容易生產/複製：將已建立的模型應用到實際數據，或大規模部署到不同的數據集時，過程會非常繁瑣，我們不需要在處理很多的問題，但Pipline可以幫助我們省略這些重複的 過程，直接呼叫fit和predict來對所有演算法模型進行訓練和預測。
* 簡化模型驗證流程：例如將pipeline 和交叉驗證結合有助於防止測試資料的統計資料外洩到交叉驗證的訓練模型中。 或與網格搜尋（Grid Search）結合，快速遍歷所有參數的結果。
# 語法
```python
sklearn.pipeline.Pipeline(steps, memory=None, verbose=False)
```

**參數詳解：**
- `steps`: 步驟，使用（key, value）列表來構建，其中 key 是你給這個步驟起的名字， value 是一個評估器物件。
- `memory`: 記憶體參數，當需要儲存Pipeline中間的"transformer"時，才需要用到memory參數，預設None。
# 實例
```python
pipe=Pipeline([('sc', StandardScaler()),('pca',PCA()),('svc',SVC())])
```