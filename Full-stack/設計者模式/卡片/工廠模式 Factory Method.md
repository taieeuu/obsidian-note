---
Date: 2023-12-29
tags:
  - "#設計者模式"
---
## 問題
![[工廠模式 Factory Method 1.png|600]]

- 可以看到他的Hardware，如果哪天開發出自己硬件，然後選擇封裝硬件訊息，這會帶來給每個用戶都需要修改代碼，把每個Hardware刪掉，在創建。
## 解決
### 工廠
![[工廠模式 Factory Method 2.png|600]]

- 我們只需在TuringFactory做改變，不需要再客戶端修改代碼
- 降低程式的耦合性
### 簡單工廠模式
工廠慢慢攥錢，擴張了，開始生產新產品。
![[工廠模式 Factory Method 3.png|600]]

- 添加了makePhone()、makeTablet()方法，這種方法很值觀，但也有很致命的，當工廠繼續擴張，會導致if else中判斷的type變多，進而讓TuringFactory變得更加的臃腫
- 那為了讓產品更好的維護，與擴張，衍生出工廠方法模式
### 工廠方法模式
![[工廠模式 Factory Method 4.png|600]]

- 與簡單方法不同的是，在簡單方法中Factory生產所有的產品，而在工廠方法中，多加了具體的方法來創建特定的產品。
## 實例

### 場景: 給一家汽車工廠，寫一個軟件來創建汽車
![[工廠模式 Factory Method 5.png|600]]

- 所有汽車都是基於Car接口，統一了汽車方法startEngine()，有兩款不同車型ModelA、ModelB，然後有一個工程主接口CarFactory定義了makeCar()方法，基於CarFactory的有ModelA、ModelBFactory

![[工廠模式 Factory Method 6.png|600]]
![[工廠模式 Factory Method 7.png|600]]
![[工廠模式 Factory Method 8.png|600]]
## [Python Code](https://github.com/turingplanet/design-patterns-series/tree/main/v12_factory_method/python)

## 注意事項

- 作用
    - 將產品的實例化延遲到具體工廠中完成
- 優缺點
    - 優點: 減低耦合性，保持了封裝性
    - 缺點: 每增加一個產品就要添家工廠類，增加系統的複雜度
- 應用場景
    - 創建對象需要使用大量重複代碼
    - 無須客戶端知道具體產品得類別及依賴關係，只需知道對應工廠就好

## 簡單工廠 vs 工廠方法 vs 抽象工廠
![[工廠模式 Factory Method 9.png|600]]