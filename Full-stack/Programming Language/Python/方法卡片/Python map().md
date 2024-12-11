---
Date: 2024-02-19
tags:
  - Python
relation:
---
# 描述
用來將一個iterable轉換到另一個iterable的函數，和comprehension不同的是不能有條件的轉換iterable，注意回傳值是一個物件，要重新轉換成iterable。
# 語法
map(函數，iterable object)

**注意:**
返回值為<class 'map'>，所以如果我們要取用他的話，可以再把她轉換為 list() 。
# 實例
```python
a = ["1","2","3","4"]

b = list(map(int, a))

print(type(b), b)

# --result--
<class 'list'> [1, 2, 3, 4]
```

