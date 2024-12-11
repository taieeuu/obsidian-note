---
Date: 
tags: 
relation:
---
# 描述

# 實例
```python
class Star(object):

    def __init__(self, name, id):
        print('__init__ is called')
        self.name = name
        self.id = id

    def __new__(cls, *args, **kwargs):
        # 打印 __new__方法中的相关信息
        print('__new__ is called')
        print(args)
        # 最后返回父类的方法
        return super(Star, cls).__new__(cls)

if __name__ == '__main__':
    star = Star('人人都是Pythonista', 91155859)

# --result--
__new__ is called
('人人都是Pythonista', 91155859)
__init__ is called

```

從上面程式碼的輸出結果不難發現__new__和__init__被調用的順序：__new__函數先被調用，建構了一個Star的實例；接著__init__函數在__new__函數返回一個實例的時候 被調用，而這個實例作為self參數被傳入了__init__函數。

除了功能上的差異外，兩者的參數也是不同的。 __new__方式的第一個參數是cls，而__init__方式的第一個參數是self。

當我們呼叫__new__時，該類別的實例還不存在（換句話說就是self所引用的物件還不存在），所以需要接收一個類別作為參數，從而產生一個實例。 而當我們在呼叫__init__時，實例已經存在，此時__init__用self作為參數，就可以對該實例進行必要的初始化操作。

這裡要注意的是：如果__new__傳回一個已經存在的實例（無論是哪個類別的），__init__方法是不會被呼叫的。

舉例:

```python
Car = 'Tesla' 

class Star(object):
    def __new__(cls):
        print("__new__ is called")
        return Car

    def __init__(self):
        print("__init__ is called")

Star()

# --result--
__new__ is called
Tesla

```

# 結論
1. `__new__`是一个类方法，它返回的是一个实例
2. `__init__`是一个实例方法，它什么都不返回
3. 只有在`__new__`返回一个新创建属于该类的实例时，当前类的`__init__`才会被调用
# 應用
**自定義實例創建邏輯：** 如果你的類需要在實例被創建之前進行一些特殊的初始化邏輯，你可以覆寫`__new__()`方法。這可能涉及到動態生成實例，根據特定條件返回現有的實例，或者執行其他自定義的創建邏輯。

```python
class MyClass:
    def __new__(cls, *args, **kwargs):
        # 自定義的創建邏輯
        instance = super().__new__(cls)
        # 其他初始化邏輯...
        return instance

```

**不可變類型：** 如果你定義的類是不可變的，即實例一旦創建就不能修改，你可能需要在`__new__()`方法中處理這種不可變性。

```python
class ImmutableClass:
    def __new__(cls, *args, **kwargs):
        instance = super().__new__(cls)
        # 初始化不可變屬性...
        return instance

```