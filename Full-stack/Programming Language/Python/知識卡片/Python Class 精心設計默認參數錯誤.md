---
Date: 2024-04-30
---
很多人在設計類別時，都會使用以後默認方式。

```python
class Player():
  def __init__(self, name, level = 0):
    self.name = name
    self.level = level
```

這樣沒有問題，但要注意的是，當今天多增加了 items 的話，他會是一個 list 型態，然而很多人都有**一個常見誤解就是當你在類別定義中使用可變物件作為預設參數時，這個預設參數是在類別定義階段創建的，並不是實例化時創建的。**

那又會有另一個疑問，如果是在__init__()裡面呢?

而在__init__()中的話，要知道 init 方法中的屬性賦值是在實例化時執行的，而不是在類定義階段執行的。

我們來看看實際例子。

```python
class Player():
  def __init__(self, name, items=[]):
    self.name = name
    self.items = items
    print(id(self.items))

p1 = Player("Alice")
p2 = Player("Bob")
p3 = Player("Charlie",["sword"])

p1.items.append("armor")
p2.items.append("shield")
print(p1.items)
```
![[Python Class 精心設計默認參數錯誤 1.png]]

實際的輸出結果，我們會發現p1、p2的 id 位置都一樣，也就代表他們之間的 list 是共享的，所以你就看到p1添加armor、p2添加shield，最後打印出p1的items時，出現了p1的item，這也就驗證我們上述所說的，在設定默認參數時，class就先evaluate一遍，把`[]`當作默認參數，換句話說這個p1和p2初始化時，這個items是使用同一個，而 list 在python 是 mutable ，所以Alice 與 Bob share 通一個 list 。

那這個問題要如何被解決呢? 我們可以使用None的方式進行初始化，然後在 init 類被實例化後，執行我們的參數默認行為。

以下圖說明
```python
class Player():
  def __init__(self, name, items=None):
    self.name = name
    if items is None:
      self.items = []
    else:
      self.items = items
    print(id(self.items))
```
![[Python Class 精心設計默認參數錯誤 2.png|600]]