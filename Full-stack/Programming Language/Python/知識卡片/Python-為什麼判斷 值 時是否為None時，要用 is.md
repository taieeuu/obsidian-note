---
Date: 2024-05-01
---
首先來看我們下面有一個 list 裡面有五個值，來遍歷看看哪些是False

```python
lst = [[], {}, set(), None, False]

for a in lst:
	if not a :
		print(a)
# ----result----
[]
{}
set()
None
False
```

上述的值都為False，所以當你使用 `if a`或者`if not a`去判斷 a 是否為None時，很有可能會判斷錯誤的情況。

下面舉一個例子

```python
class FibCalculator:
  def __init__(self, cache=None):
    if cache:
      self.cache = cache      
    else:
      self.cache = {}

  def fib(self, n):
    if n in self.cache:
      return self.cache[n]
    if n <= 2:
      result = 1
    else:
      print(n)
      result = self.fib(n-1) + self.fib(n-2)
    self.cache[n] = result
    return result

cache={}
fc1 = FibCalculator(cache)
fc2 = FibCalculator(cache) 
print(fc1.fib(10))
print(fc2.fib(10))

# ----result----
10
9
8
7
6
5
4
3
55
10
9
8
7
6
5
4
3
55
```

我們會發現 fc1、fc2 之間的cache沒有被分享，原因是因為我們的`if cache`，因為這裡的`if cache`除了在cache是None時不成立之外，在cache是空的dictionary時也不成立。

所以下列程式碼我們就可以把它改成這樣，讓他有cache數據共享的功能。

```python
class FibCalculator:
  def __init__(self, cache=None):
    if cache is None:
      cache = {}
    self.cache = cache
    
  def fib(self, n):
    if n in self.cache:
      return self.cache[n]
    if n <= 2:
      result = 1
    else:
      print(n)
      result = self.fib(n-1) + self.fib(n-2)

    self.cache[n] = result
    return result
```

而我們知道Python有許多內置的數據結構，在某些特殊情況下會被evaluate成False，而用戶自定義的數據結構，可以通過像是`__bool__`這個magic method的重載，也可以自定義他甚麼時候是True。

```python
class MydataStructure:
	def __bool__(self):
		return False
d = MydataStructure()
if not b:
	print("Its None")

#----result----
Its None
```

雖然實際的代碼部會像這樣，但是確實有些時候的數據結構會對`__bool__`進行重載，導致你判斷出現錯誤的情況。

接下來我們 == 的情況

```python
lst = [[], {}, set(), None, False]

for a in lst:
	if a == None:
		print(a)
# ----result----
None
```

可以發現，這次確實被挑出來了，那會代表他使用的方式跟 `is None` 好嗎，但要知道 == 在python中也是會被重載的，所以跟剛剛所討論的`__bool__`是同一個問題，下面舉例。
```python
class MydataStructure():
	def __eq__(self):
		return True
a = MydataStructure()
if a==None:
	print(a)

#----result----
Its None 
```

```python
class MydataStructure():
	def __eq__(self):
		return True
a = MydataStructure()
if a is None:
	print(a)

#----result----

```

因為有重載這個風險在，你也不知道其他人會不會使用這樣的寫法，假設有剛好有這樣的寫法，就會導致你會花費大量的時間去察看錯誤，以及去DEBUG，所以我們在一開始的時候使用`is None`來做可以免去之後有機會發生的錯誤。

從字節碼來看
```python
import dis

print(dis.dis('a == None'))
print(dis.dis('a is None'))
```
![[Python-為什麼判斷 值 時是否為None時，要用 is 1.png|500]]

可以看到它們的差距在於`COMPARE_OP`與`IS_OP`，我們可以看到下面的圖示，`is`是直接比較兩個object的地址`((left == right)^oparg)`，因為使用指針去對記憶體位置的直接比較，所以會比一般的參數比較要來的快。

舉例來說
>
根據物件的內容來進行比較，而不僅僅是比較物件在記憶體中的位址。 例如：
== ：用來檢查兩個物件的值是否相等，即使它們是不同的物件。
<、>等操作符：用來比較兩個物件的大小。 物件的比較規則可以透過自訂的方法來確定。
而is操作符則不同，它檢查兩個物件是否是同一個記憶體位置。 即使兩個物件的內容相同，但如果它們在記憶體中是不同的位置，is會傳回False。

![[Python-為什麼判斷 值 時是否為None時，要用 is 2.png|466]]

那在觀看到 `COMPARE_OP`，這裡使用的是PyObject_RichCompare，進去之後會發現相對的複雜，如果參與比較你使用內置的數據結構，或是像是magic method的話，速度相對是還可以接受的，否則他還會去調用一個python的函數，這樣就又會相對來說變慢了許多。
![[Python-為什麼判斷 值 時是否為None時，要用 is 3.png|500]]

### 總結
儘管在現在程式中，這樣的差別並不明顯，但我們犧牲這一微小的速度沒有喚回任何東西，甚至在有些情況下會出錯，所以以後在判斷任何情況下是不是None時，直接用`is`或者`is not`。