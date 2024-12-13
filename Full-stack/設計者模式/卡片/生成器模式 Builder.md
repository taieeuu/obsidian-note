---
Date: 2023-12-29
tags:
  - 設計者模式
---
## 意圖
![[生成器模式 1.png|600]]

## 問題
![[生成器模式 2.png|600]]

- 第一種: 左
    - 當有眾多功能時，功能的組合會越來越多，導致需要管理非常多的類
- 第二種: 右
    - 把需要創建功能的選項放入置Car的參數中，當需要創建時，設為True，反之。
    - 眾多的功能，會導致創建時許多的功能用不到，這對於構造函數很不簡潔。
## 解決
![[生成器模式 3.png|600]]

- 使用添加特定函數的方式，來代表需要的功能，這樣可以讓代碼更加的簡潔
## 生成器模式代碼結構
![[生成器模式 4.png|600]]

- 主要兩個具體的Builder1、Builder2創建產品有Product1、Product2
- Director雖然不是必要，但在生成器中非常有用，他可以規定一系列特定的步驟來創建產品，以便在程序中可以重複使用
## 實例
### 場景: 給一家奶茶店來創建點餐系統

#### 實例架構
![[生成器模式 5.png|600]]
## [Python Code](https://github.com/turingplanet/design-patterns-series/tree/main/v11_builder/python)
## 注意事項

- 主要作用
    - 逐步構建複雜的對象
- 應用場景
    - 蓋房子、訂製牛肉麵、訂製汽車
- 優點
    - 分布創建，更加靈活
    - 可以複用相同的製作代碼
    - 嚴格遵循單一職責原則
- 缺點
    - 代碼整體複雜度增加
## 生成器 vs 抽象工廠
![[生成器模式 6.png|600]]