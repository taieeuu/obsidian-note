---
Date: 2024-02-21
tags:
  - Python
relation:
---
## Python以物件(object)的方式看待、處理資料

Python是一種`物件導向程式語言`，從資料、變數到函式，都被視為「物件」(Object)來處理，物件包含以下元素

- `名稱`：用來稱呼這個物件
- `資料型態`: 設定物件的用途
- `唯一的id`: 用來區別不同物件
- `值`：真正的資料內容
- `參考計數`: 追蹤這物件被使用的頻率

例如宣告一個變數`x = 1`，在Python的設計觀點：

- 使用者宣告了一個`變數物件`，賦予`名稱`為`x`
- 系統賦予一個識別用的id
- 這個物件的資料值指定為`1`
- 因為存放數值`1`，此物件資料型態是`integer`

以上是Python內建的資料型別變數物件，在這個章節中，我們將介紹，使用者怎麼自訂物件，處理客製化資料。

## 什麼是物件

`物件(object)`是一種資料型態，由`屬性（Attribute)`與`方法(Method)`組成。

屬性以**變數**形式表示，一個物件可以包含多個屬性，例如一個多邊形物件的顏色、長度。

方法以**函式**來表示，一個物件可以包含多個函式，例如一個多邊形物件可以被計算面積、體積

## 什麼是類別

`類別（Class）`，就是用來建立物件的方法。

你必須定義類別，來指出一個物件包含哪些屬性跟方法。

### 定義類別

例如你想定義一個銀行帳號的物件

- 屬性包括帳號代碼（number）跟戶名（name)、跟餘額(balance)
- 方法包括存款（deposit）與提款（withdraw）  
    參考語法如下。

```python
class Account:
    def __init__(self, number, name):
        self.number = number
        self.name = name
        self.balance = 0
        
    def deposit(self, amount):  #存款動作: amount代表存入金額
        if amount <= 0:
            print('must be positive')
        self.balance += amount
        
    def withdraw(self, amount): #取款動作: amount代表取款金額
        if amount <= self.balance:
            self.balance -= amount
        else:
            print('balance not enough')
```

### 從類別中建立物件

通常也被稱為`實例(Instance)`

延續上面的程式碼，我們建立兩個帳號資料物件，分別是：

- Account1
    - number:`S00001`
    - name:`AAA`
- Account2
    - number:`S00002`
    - name: `BBB`

參考程式碼如下：

```
class Account:
    def __init__(self, number, name):
        self.number = number
        self.name = name
        self.balance = 0
        
    def deposit(self, amount):  #存款動作: amount代表存入金額
        if amount <= 0:
             print("Error!")
        self.balance += amount
        
    def withdraw(self, amount): #取款動作: amount代表取款金額
        if amount <= self.balance:
            self.balance -= amount
        else:
            print("Error!")

#建立account 1
account1 = Account("S00001", "AAA")
account1.deposit(1000)              #存入1000
print(account1.balance)

#建立account 2
account2 = Account("S00002", "BBB")
account2.deposit(2000)              #存入2000
print(account2.balance)
```

### 不預先定義屬性的作法

Python允許在物件生成階段，再進行屬性的定義，例如下方例子的第16, 17行。

```python
class Account:
    pass

def deposit(acct, amount):
    if amount <= 0:
        raise ValueError('must be positive')
    acct.balance += amount
       
def withdraw(acct, amount):
    if amount <= acct.balance:
        acct.balance -= amount
    else:
        raise RuntimeError('balance not enough')
        
acct = Account()
acct.number = '123-456-789'
acct.name = 'Justin'
acct.balance = 0

print(acct.number)    # 123-456-789
print(acct.name)      # Justin

deposit(acct, 100)
print(acct.balance)   # 100
withdraw(acct, 50)
print(acct.balance)   # 50
```

### 隨堂練習

請宣告一個class `CRectangle`(長方形類別)

- 它的屬性包括長`width`跟寬`height`
- 函式是`area(width, height)`，用來計算面積

請使用此class，生成一個長寬2x3的`rectangle1`物件，並且呼叫他的`area`方法，輸出它的面積

參考程式碼

### 類別的繼承

我們從這個例子出發，有兩個class，都有`drive`這個方法，根據物件導向設計原則，我們應該使用`繼承`這個技巧，來優化程式碼，方便後續維護。

```python
# 汽車類別
class Car:
    # 駕駛方法
    def drive(self):
        print("drive method is called.")
    # 加速方法
    def accelerate(self):
        print("accelerate method is called.")
# 飛機類別
class Airplane:
    # 駕駛方法
    def drive(self):
        print("drive method is called.")
    # 飛行方法
    def fly(self):
        print("fly method is called.")
```

繼承作法如下，我們新增一個`class` Transpotation，也就是交通工工具的類別，然後讓原本的兩個`class`Car, Airplane繼承它，所以這兩個class都不用再實作自己的`drive`方法了。

```python
# 交通工具(基底類別)
class Transportation:
    # 建構式
    def __init__(self):
        self.color = "white"  #顏色屬性
    # 駕駛方法
    def drive(self):
        print("drive method is called.")
# 汽車子類別
class Car(Transportation):
    # 加速方法
    def accelerate(self):
        print("accelerate is method called.")
# 飛機子類別
class Airplane(Transportation):
    # 飛行方法
    def fly(self):
        print("fly method is called.")

```

### 方法覆寫(Method Overriding)

當子類別中定義了和父類別同名的方法(Method)，這時候子類別的物件(Object)呼叫這個同名方法時，其中的實作內容將會覆蓋掉父類別的同名方法，這就叫做方法覆寫(Method Overriding)，如下範例：

```python
# 交通工具(基底類別)
class Transportation:
    # 駕駛方法
    def drive(self):
        print("Base class drive method is called.")
# 汽車子類別
class Car(Transportation):
    # 駕駛方法
    def drive(self):
        print("Sub class drive method is called.")
        
car1 = Car()
car1.drive()
```

如果我們想在子類別中執行父類別的方法時，則可以使用`super()`內建方法來達成，如下範例：

```python
# 交通工具(基底類別)
class Transportation:
    # 駕駛方法
    def drive(self):
        print("Base class drive method is called.")
# 汽車子類別
class Car(Transportation):
    # 駕駛方法
    def drive(self):
        super().drive()
        print("Sub class drive method is called.")
        
car1 = Car()
car1.drive()
```

從執行結果可以看到，子類別透過`super()`內建方法執行父類別的`drive()`方法後，接著執行子類別的方法後續實作。

### 隨堂練習

請創造一個class，`CPolygon`多邊形

- 屬性有`line1`, `line2`
- 方法有`area()`計算面積

再宣告兩個class，`CTriangle`與`CRectangle`，繼承`CPolygon`，各自實作自己的`area()`進行面積計算。

- `CTriangle.area()`: 三角形面積公式
- `CRectangle.area()`: 長方形面積公式

請使用以上兩個class，各自生成物件`triangle1(2,3)`和`rectangle1(2,3)`，並且使用它們的方法，計算面積並輸出。

參考程式碼

```python
class CPolygon:
    def __init__(self, line1, line2):
      self.line1 = line1
      self.line2 = line2    

    def area(self):
        print("area")

class CTriangle(CPolygon):
    def area(self):
        area = (self.line1 * self.line2)/2
        return area

class CRectangle(CPolygon):
    def area(self):
        area = self.line1 * self.line2
        return area

triangle1 = CTriangle(2,3)
print(triangle1.area())

rectangle1 = CRectangle(2,3)
print(rectangle1.area())
```

## 類別的進階使用技巧

- 多重繼承
- 屬性存取
- 聚合與組合



