---
Date: 2024-02-19
tags:
  - Python
relation:
---
# 描述
它可以讓你快速地過濾出你想要的資料。它可以讓你從一個資料序列中篩選出符合條件的資料，而不需要你自己寫迴圈來處理。
# 語法
filter(function, iterable)

filter() 函式會將 iterable 中的每一個元素傳入 function 中，如果 function 回傳 True，則該元素會被保留，如果回傳 False，則該元素會被過濾掉。最後，filter() 函式會回傳一個由符合條件的元素組成的資料序列。
# 實例
```python
def is_even(x):
    return x % 2 == 0

nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

even_nums = list(filter(is_even, nums))

print(even_nums)

#---result--
[2, 4, 6, 8, 10]
```

```python
nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

even_nums = list(filter(lambda x : x&1==1, nums))

print(even_nums)

#---result--

[1, 3, 5, 7, 9]
```