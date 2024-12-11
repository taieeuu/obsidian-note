---
Date: 2023-12-29
tags:
  - "#設計者模式"
---
## 原則

==**提供創建一系列膝關或相互依賴對象的接口**==

## 問題
![[抽象工廠模式 Abstract Factory 1.png|600]]

>如果每個都單一創建，有可能會創建出風格不一的情況，導致顧客的不滿意。

## 解決方案
- 提供一個創建一系列相關或者相互依賴對象的接口，而無指定他們具體的類
- 抽象工廠模式將一組對象的實現細節與它們的一般使用分離開來

![[抽象工廠模式 Abstract Factory 2.png|600]]
## 代碼框架
![[抽象工廠模式 Abstract Factory 3.png|600]]
## 實例
### Abstract_Factory
![[抽象工廠模式 Abstract Factory 4.png|600]]
![[抽象工廠模式 Abstract Factory 5.png|600]]
![[抽象工廠模式 Abstract Factory 6.png|600]]

- BMW Factory and Tesla Factory都是基於CarFactory接口的一個類
- BMWM5、TeslaModelS 基於Sedan、TeslaModelY、BMWX5基於SUV
- 不需要了解產品實際細節，只需要調用factory統一接口來創建實例就可以了
## [Python Code](https://github.com/turingplanet/design-patterns-series/tree/main/v10_abstract_factory/python)
## 適用性合優點
- 適用性
- 適用場景:
    - 遊戲服裝套裝，通常一整套一起換
    - 操作系統的組件模塊，同一組模塊需要對應相同的系統
- 優點:
    - 規範了創建相同系列產品方式
    - 只需要暴露創建街口，可以隱藏實現細節
    - 容易改變產品系列
- 缺點:
    - 擴展產品困需要額外工作: 除了產品代碼外，還需要改動Factory