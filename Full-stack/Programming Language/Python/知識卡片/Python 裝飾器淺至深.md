---
Date: 2024-05-06
---
## 如何使用
1. 基本概念
```python 
def testDecorator(callback):
	def innerFunc():
		print("裝飾器")
		callback()
	return innderFunc
@testDecorator
def decoratedFunc():
	print("普通函式")
# ----result----
裝飾器
普通函式
```
2. 範例適用
```python
def cal(callback):
	def run():
		result = 0
		for n in range(51):
			result+=n
		callback(result)
	return
@cal
def show(n):
	print("計算結果", n)
#----result----
計算結果 1275
```

在python 我們知道所有的東西都是object，而自己定義的函數也不例外，函數在python中也是一個普通的object而已。

我們可以來看一下Python中編譯之後的字節碼。
```python
import dis
# 創建一個包含函數的完整模塊代碼
module_code = """
def double(x):
    return x * 2
"""

# 編譯模塊代碼
compiled_code = compile(module_code, '<string>', 'exec')

# 使用 dis 模塊來反組譯整個編譯後的代碼
dis.dis(compiled_code)
```

![[Python 裝飾器淺至深 1.png]]

出來的結果如上圖，我們可以發現函數體的部分(下半部)被編譯成一個object (<code object double at 0x...>)，然後上半部分他只是把這個object給 load ，再把名稱double也給 load 進來，然後做了一個make function，也就是把這個code object變成了一個有名稱的fucntion object，且保存到了double的名字中，所以當我們在Python中定義一個function時，我們只是新增一個變量而已，然後這個變量裡面保存一個函數對象(code function)。

```python
import dis

def double(x):
  return x * 2
print(double)
```
![[Python 裝飾器淺至深 2.png]]
函數對象中有一個特點，就是他是一個callable，在python中的callable，意味者後面可以加上一個小括號，然後去調用他。以上述的程式碼來說。我們定義了一個double，需要傳進一個參數x。

實際上，在python語法中，任何的對象你都可以調用，但是這個東西如果不是callable的話，他在runtime會出錯，下圖所示。我們可以發現他其實也是可以編譯通過，但她會給你一個syntax warning，字節碼可以看到她一樣 load 完這個 1 之後，然後再嘗試call他。
```python
print(dis.dis("1()"))
```
![[Python 裝飾器淺至深 3.png]]
## 函數傳遞對象
```python
def double(x):
  return x * 2

def triple(x):
  return x * 3
  
def calc_number(func, num):
  print(func(num))
  
print(calc_number(double, 2))
print(calc_number(triple, 3))

#----result----
6
9
```

所以你就可以知道函數完全可以像其他變數一樣被傳去其他的函數，那同樣的，函數不光可以作為變量傳進其他函數中，也可以做為返回值

```python
def get_multiple_func(n):

    def multiple(x):

        return x * n

    return multiple

double = get_multiple_func(2)
triple = get_multiple_func(3) 

print(double(2))
print(triple(3))

# ----result----
6
9
```
>[!info]
>get_multiple_func()有一個parmeter，且他返回是multiple一個function ，而multiple返回什麼值是由get_multiple_func(n)來決定的。
>也就是說get_multiple_func(2)返回double這一個函數，get_multiple_func(3)返回是一個triple這一個函數，而這兩個參數都是可被調用，也就是callable，可以看到我們在print()中正常的調用他們。

所以如果函數可以作為一個參數被傳進其他函數，你也可以理解函數的返回值可以是一個函數，那decorator就變得非常容易理解些了。

## decorator
那麼decorator本身是一個callable，他沒有特殊的地方，在一開始時你可以把decorator理解成一個function
```python
# 創建包含兩個函數的完整模塊代碼
module_code = """
def dec(x):
    pass

@dec
def double(x):
    return x * 2
"""

# 編譯模塊代碼
compiled_code = compile(module_code, '<string>', 'exec')

# 使用 dis 模塊來反組譯整個編譯後的代碼
dis.dis(compiled_code)
```
![[Python 裝飾器淺至深 4.png]]

反藍的部分，就是我們decorator起作用的部分，可以發現他與我們定義double函數是一樣的，而真正起作用是這裡的 CALL_FUNCTION 跟 STORE_NAME 在和前面的 LOAD_NAME來看，可以發現他只是一種語法糖而已，也就是下述的寫法。

```PYTHON
def dec(x):
    pass

@dec
def double(x):
    return x * 2
# 完全等價於
double = dec(double)
```

我們先看看極端的例子
```python 
def dec(f):
	return 1
@dec
def double(x):
	return x * 2
# 完全等價於
double = dec(double)
print(double)

# ----result----
1
```
> [!info]
> 我們從完全等價來看，dec(f)返回的是一個1，1被覆值給了double，且print(double)，所以也就是 1。

在大部分情況下，我們使用decorator時，還是認為decorator是一個參數是函數，返回值也是函數的這麼一個函數

```python
import time

def timeit(f):
    def wrapper(x):
        start = time.time()
        ret = f(x)
        print(time.time() - start)
        return ret
    return wrapper

@timeit
def my_func(x):
    time.sleep(x)
my_func(1)

#----result----
1.0011062622070312

```
>[!info]
>我們可以看到timeit是一個函數，且把它當作decorator來使用，首先他有一個 f 的參數，且返回了一個wrapper也是一個函數，wrapper裡面，一開始計時，然後調用timeit給進來的這個參數 f ，記錄返回值，打印，再返回。也就是提供一個計時的功能。

## 使用decorator好處
當你有不同的函數時，你都可以比較簡潔的來完成這件事情
舉例
```python
import time

def timeit(f):
    def wrapper(x):
        start = time.time()
        ret = f(x)
        print(time.time() - start)
        return ret
    return wrapper

@timeit
def my_func(x):
    time.sleep(x)

@timeit
def other_func(x):
    return x * 2

print(other_func(2))
# ----result----
0.0
4
```
> [!info]
> 運行時發費了0.0，且返回值打印4

## 正常用法
```python 
def timeit(f):
	def wrapper(*args, **kwargs):
		start = time.time()
        ret = f(*args, **kwargs)
        print(time.time() - start)
        return ret
    return wrapper
@timeit
def my_func(x):
	time.sleep(x)
def add(x, y):
	return x+y
print(add(2, 3))

# ----result----
0.0
5
```
### 帶參數的decorator
```python 
@timeit(10)
def double(x):
	return x * 2
#等價
double = timeit(10)(double)
```
>[!info]
>他就是相當於等價過程中，多了一次函數的調用，不調用參數是直接timeit(double)，現在多了參數，於是會變成先計算timeit(10)，**注意**這裡返回是一個函數，然後再去調用double，再返回一個函數，所以他們也是完全等價。

**實現**
```python
import time

def timeit(iteration):
    def inner(f):
        def wrapper(*args, **kwargs):
            start = time.time()
            for i in range(iteration):
                ret = f(*args, **kwargs)
            print(time.time() - start)
            return ret
        return wrapper
    return inner

@timeit(1000)
def double(x):
    return x * 2
#等價
inner = timeit(1000)
double = inner(double)
```
> ![info]
>最外層我們使用的decorator傳進了一個iteration是運行多少次，而他返回是一個inner的函數，且他還take的是一個函數，返回的也是一個函數，而這個函數wrapper裡面是運行了iteration次的函數 f。
>首先timeit(1000)拿到一個inner這個函數，然後inner對double做一個調用，返回了一個函數保存到double裡面。