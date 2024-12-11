---
Date: 
tags: 
relation:
---
# 前言
Python 默認所有成員方法和屬性是Public的，想要私有化需再變量前加上 " __ "两个下划线，只要私有化了，就無法從外部調用。

>[!info]
>**好處**
>1. 封裝變嚴謹，告訴使用者這個函數是你無法使用的。

```python=
class A:
	def __init__(self):
		self.__V = 0
	def __print_v(self):
		print(self.__v)

o = A()
o.__print_v()

## --result--
AttributeError:'A' object has no attribute '__print_v'
```
