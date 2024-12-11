---
Date: 2024-03-11
tags:
---
# 基本報表
```python
import sweetviz as sv
report_train = sv.analyze([train, 'train']) # 'train'是指會給這個資料集命名為train
report_train.show_html(filepath='Basic_train_report.html') # 儲存為html的格式
```
# 加入目標變數(Y定義)的報表
加入目標變數前，可以先給予資料一個正確的格式，否則其會自動判定特徵的屬性
```python
feature_config = sv.FeatureConfig(skip='Name',  # 要忽略哪個特徵
                                  force_cat=['Pclass', 'Sex', 'SibSp', 'Parch', 'Ticket', 'Cabin'], # Categorical特徵
                                  force_num=['Age', 'Fare'], # Numerical特徵
                                  force_text=None) # Text特徵
```

```python
report_train_with_target = sv.analyze([train, 'train'],
                                     target_feat='Survived', # 加入特徵變數
                                     feat_cfg=feature_config)
                                     
report_train_with_target.show_html(filepath='Basic_train_report_with_target.html')
```
# 比較train和test的報表
比較在train資料集下，分成Male和Female兩個資料集的樣態是否有不同
缺點是只能分割一個binary的特徵

```python
compare_report = sv.compare([train, 'Training Data'], # 使用compare
                            [test, 'Test Data'],
                            'Survived',
                            feat_cfg=feature_config)

compare_report.show_html(filepath='Compare_train_test_report.html')
```
![[Sweetviz 套件資料分析 1.png]]
# 比較Male和Female兩個子資料集的報表
來到了最後一個報表，剛剛上面的報表是在比較兩個不同的母資料集的樣態，而現在這個報表目的是在比較同個母體中，一個二元的X變數所代表的兩個不同的資料集的樣態，這邊以男和女為例，可以更進階的去分析，是否男生和女生在其他的X變數中會有不一樣的樣態。
```python
compare_subsets_report = sv.compare_intra(train,
                                          train['Sex']=='male', # 給條件區分
                                          ['Male', 'Female'], # 為兩個子資料集命名 
                                          target_feat='Survived',
                                          feat_cfg=feature_config)

compare_subsets_report.show_html(filepath='Compare_male_female_report.html'
```
![[Sweetviz 套件資料分析 2.png]]
# 參考
https://medium.com/jackys-blog/sweetviz-%E4%B8%80%E5%80%8B%E5%9C%A8%E8%B3%87%E6%96%99%E5%88%86%E6%9E%90%E5%89%8D%E5%BF%85%E9%A0%88%E5%85%88%E4%BD%BF%E7%94%A8%E7%9A%84%E5%A5%97%E4%BB%B6-2bdefa8e5e68