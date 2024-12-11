---
Date: 2024-02-15
tags:
  - Python
---
## @property
用來定義屬性的getter方法`area`。該`area`屬性提供了一種訪問正方形區域的方法，無需像方法那樣調用它，而是像屬性一樣調用它。

一般訪問要 obj.area()，使用後則 obj.area。

```python
class square(): 
	def __init__(self. sideLen): 
	self.__sideLen = sideLen 
	@property 
	def area(self): 
		return self.__sideLen ** 2
		 
obj = Square(10) 
print(obj.area)
```
**好處**
1. 封裝和資料隱藏性: 封裝和資料隱藏：透過使用雙下劃線（`__sideLen`），你可以建立`sideLen`一個私有屬性，並且不能從類別外部直接存取它。這有助於封裝物件的內部狀態並促進資料隱藏，這是物件導向程式設計的基本原則。
    
2. 可讀性和簡單性：裝飾器允許您使用語法而不是 來`@property`存取正方形的區域。這可以產生更具可讀性和自然性的程式碼，使其更易於理解和維護。`obj.area``obj.area()`
    
3. 一致性：透過將區域公開為屬性，您可以提供用於存取計算值的一致介面。類別的使用者可以像對待任何其他屬性一樣對待它，從而減少記住是否在方法呼叫中使用括號的需要。
## @classmethod
實例方法與屬性的特色是有self，屬性開頭是self，同時所有方法的第一個參數是self，這些是建立類別物件時屬於物件的一部分。先前所述皆是實例方法，使用時須建立類別物件，然後由物件調用。

**類別方法前面則是@classmethod，所不同的是第一個參數習慣是`cls`。類別方法與屬性不需要實例化，他們可以由類別本身直接調用**。另外類別屬性戶隨時被更新。
## @staticmethod
靜態方法是由@staticmethod開頭，**不需原先的self或cls參數，這只是碰巧存在類別的函數，與類別方法和實例方法沒有綁定關係，這個方法也是由類別名稱直接調用。**
```python
class MyClass: 
	@staticmethod 
	def my_static_method(arg1, arg2):
	 # method body 
	 pass
	 
# 無需實例化
MyClass.my_static_method(arg1, arg2)
```

**重點:**
1. 無法存取實例屬性：由於靜態方法無法存取實例屬性 ( `self`)，因此它們無法修改或存取任何特定於實例的資料。
    
2. 無隱式第一個參數：與實例方法不同，靜態方法沒有隱式第一個參數`self`。相反，它們的行為就像常規函數一樣。
    
3. 類別級邏輯：靜態方法通常用於定義與類別相關的實用函數，但不依賴特定的實例資料。