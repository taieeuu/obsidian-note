---
Date: 2024-02-18
tags:
  - Python
relation:
---
# --doc--
返回方法註解

```python
def getMax(x, y):
	'''123'''
	if int(x)>int(y):
		return y
	return x

print(getMax.__doc__)

# ---result--
123
```

# --name--
顯示在本身程式檔案中名稱，如果是該檔案則是__main__

# --str--
協助返回一讀取字串

```python
class Name:
	def __init__(self, name):
		self.name = name

a = Name("Nung")
print(a)

# --result--
<__main__.Name object at 0x3423423>
```

由於上述沒有定義`__str__()` 方法下，我們獲得了一個不容易閱讀結果。

```python
class Name:
	def __init__(self, name):
		self.name = name
	def __str__(self):
		return f"{self.name}"
a = Name("Nung")
print(a)

# --result--
Hung
```

但如果在shell中調用 a ，則會回傳<__main__.Name object at 0x3423423>，一樣會獲得不容易閱讀結果。如果要改善，就會需要使用__repr__()方法。
# --repr--
如果只是在shell使窗讀入類別變數 a ，系統只是呼叫__repr__()方法做回應，為了要獲得容易閱讀結果，也需要定義此方法。
```python
class Name:
	def __init__(self, name):
		self.name = name
	def __str__(self):
		return f"{self.name}"
	__repr__ == __str__
a = Name("Nung")
print(a)
```
# --iter--
建立類別時也可以將類別定義成一個迭代物件，類似 list 或 tuple，供 for in 循環內做使用，這時類別需要設計 next() 方法，取下一個值，直到結束條件，可以使用 raise StopIteration。
```python
>>> class MyIterator:

        def __init__(self):
            self.num = 0
        # iter()和next()方法使这个类变成迭代器
        def __iter__(self):
            # 类本身就是迭代器，故直接返回本身
            return self
        def __next__(self):
            # 返回当前值
            return_num = self.num
            # 并改变下一次调用的状态
            self.num += 2
            return return_num
```
# --eq--
了解2個字串是否相同

```python
class City():
	def __init__():
		self.name = name
	def equals(self, city2):
		return self.name.upper() == city2.name.upper()
		
one = City("Taipei")
two = City("taipei")
three = City("myhome")
print(one.equals(two))
print(one.equals(three))

#--result--
True
False
```

### 還有其他加減乘除等方式