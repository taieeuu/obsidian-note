---
Date: 2024-02-19
tags:
  - Python
relation:
---
# 描述
用途: 當函數變數的數量不固定時
# 語法
```python
def f(*args, **kwargs)
```

- 主要運用在使用者輸入資料長度可變動，長度也可為0。
- args 是 arguments 縮寫，表示位置參數；  
    kwargs 是 keyword arguments 縮寫，表示關鍵字參數。
- * args 中的args可以自己命名，同理** kwargs。
- 兩者**並行時，* args必須置於 **kwargs之前。**
# 實例
```python
def fun(a, *args, **kwargs): 
	print("a={}".format(a)) 
	for arg in args: 
		print('Optional argument: {}'.format( arg ) ) 
	for k, v in kwargs.items(): 
		print('Optional kwargs argument key: {} value {}'.format(k, v))


fun(1,22,33, k1=44, k2=55)

#--result--
a=1 
Optional argument: 22 
Optional argument: 33 
Optional kwargs argument key: k1 value 44 
Optional kwargs argument key: k2 value 55

```

