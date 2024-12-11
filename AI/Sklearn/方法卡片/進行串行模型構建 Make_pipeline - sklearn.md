---
Date: 2024-03-23
tags:
  - machinea_learning
  - 方法
  - Sklearn
---
# 描述
用於創建一個簡化的機器學習pipeline。 它接受一系列的轉換器和估計器作為參數，並自動為它們產生唯一的名稱。 這種方式省去了手動為每個步驟指定名稱的步驟，使pipeline的創建更加便捷。
# 實例
```python
from sklearn.pipeline import make_pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression

# 创建一个简化的流水线
pipeline = make_pipeline(StandardScaler(), LogisticRegression())
```