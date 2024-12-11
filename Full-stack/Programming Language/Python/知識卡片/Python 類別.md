---
Date: 2024-02-15
tags:
  - Python
---
## 衍生類別與基底類別有相同名稱的方法
衍生類別也可以有自己的方法，同時也有可能衍生類別的方法名稱和基底類別方法名稱重複，**碰上這個狀況Python會優先找尋衍生類別是否有這個名稱**，如有則先使用，如果沒有則使用基底類別的名稱內容。
```python
class Person():
	def job():
		print("我是老師")
class LawerPerson(Person):
	def job(self):
		print("我是律師")
hung = Person()
ivan = LawerPerson()
hung.job()
ivan.job()
```
*輸出:*
```
我是老師
我是律師
```
## 衍生類別`引用`基類的方法
經過初始化處理後，mydog.name將由 "lily" 變為 "My pet lily"

```python
class Animals():
	def __init__(self, animal_name, animal_age):
		self.name = animal_name
		self.age = animal_age
	def run(self):
		print(self.name.title(), "is running")

class Dogs(Animals):
	def __init__(self, dog_name, dog_age):
		super().__init__("My pet " + dog_name.title(), dog_age)
mycat = Animals('lucy', 5)
print(mycat.name.title(), ' is ', mycat.age, ' years old.')
mycat.run()

mydog = Dogs('lily', 6)
print(mydog.name.title(), ' is ', mydog.age, ' years old. ')
mydog.run()
```
*輸出*
```
Lucy is 5 years old.
Lucy is running
My pet Lily is 6 years old.
My pet Lily is running
```
## 兄弟類別屬性取得
如果Ivan類別想取得Ira類別的屬性iramoney可使用下面注意寫法的部分

```python
class Father():
	def __init__(self):
		self.fathermoney = 1000
class Ira(Father):
	def __init__(self):
		self.iramoney = 8000
		super().__init__()
class Ivan(Father):
	def __init__(self):
		self.ivanmoney = 3000
		super().__init__()
	def get_money(self):
		print("Ivan資產: ", self.ivanmoney,
			"\n 父類資產: ",self.fathermoney,
			"\n Ira資產: ", Ira().iramoney)  **注意寫法**
```
## 多重繼承
會先使用寫在前方的基類

```python 
class Grandfather():
	def action1(self):
		print("Grandfather")
class Father(Grandfather):
	def action2(self):
		print("Father")
class Uncle(Grandfather):
	def action2(self):
		print("Uncle")
class Ivan(Father, Uncle):
	def action3(self):
		print("Ivan")

ivan = Ivan()
ivan.action3()   #順序 Ivan
ivan.action2()   #順序 Ivan -> Father
ivan.action1()   #順序 Ivan -> Father -> GrandFather
```
*輸出*
```
Ivan
Father
GrandFather
```

### 還有一種情況
```python
class Grandfather():

    def action1(self):

        print("Grandfather")

    def a(self):

        print("Grandfather a")

class Father(Grandfather):

    def action2(self):

        print("Father")

class Uncle(Grandfather):

    def a(self):

        print("Uncle a")

    def action2(self):

        print("Uncle")

class Ivan(Father, Uncle):

    def action3(self):

        print("Ivan")

  

ivan = Ivan()

ivan.action3()   #順序 Ivan

ivan.action2()   #順序 Ivan -> Father

ivan.a()   #順序 Ivan -> Father -> Uncle -> a

# --result--
Ivan
Father
Uncle a
```
## super()應用在多重繼承問題
從( 1 ) 可以發現 C 類別的第2參數B類別沒有被啟動。這邊Python其實使用super()的多重繼承，在此算是協同作業，我們必須在基類也噌家super()設定，才可以正常作業。看 ( 2 )。

( 1 ) 
```python
class A():
	def __init__(self):
		print('class A')
class B():
	def __init__(self):
		print('class B')
class C(A, B):
	def __init(self):
		super().__init__()
		print('class C')

x = C()
```
*輸出*
```
class A
class C
```

( 2 )
```python
class A():
	def __init__(self):
		super().__init__()
		print('class A')
class B():
	def __init__(self):
		super().__init__()
		print('class B')
class C(A, B):
	def __init(self):
		super().__init__()
		print('class C')

x = C()
```
*輸出*
```
class B
class A
class C
```
## 回傳物件的類別是否屬於某一個類別 - isinstance()
```python 
class Grandfather():
	pass
class Father(Grandfather):
	pass
class Ivan(Father):
	def fn(self):
		pass
grandfa = Grandfather()
father = Father()
ivan = Ivan()
print('ivan屬於ivan類別: ', isinstance(ivan, Ivan))
print('ivan屬於Father類別: ', isinstance(ivan, Father))
print('ivan屬於Grandfather類別: ', isinstance(ivan, Grandfather))
print('father屬於ivan類別: ', isinstance(Father, ivan))
```
*輸出*
```
ivan屬於ivan類別: True
ivan屬於Father類別: True
ivan屬於Grandfather類別: True
father屬於ivan類別: False
```
## Child & Parent
Child(Parent)的語法是指Child自動繼承Parent的屬性，但如果程式碼中有特別指定child的參數則以該參數為主

```python
class parent(object):  
	x=1  
class child1(parent):  
	pass  
class child2(parent):  
	pass  
print(parent.x,child1.x,child2.x) >>(1,1,1)  
child1.x=2  
print(parent.x,child1.x,child2.x) >>(1,2,1)  
parent.x=3  
print(parent.x,child1.x,child2.x) >>(3,2,3)
```

