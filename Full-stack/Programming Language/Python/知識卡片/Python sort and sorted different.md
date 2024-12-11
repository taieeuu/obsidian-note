---
Date: 2024-03-22
tags:
  - Python
---
```python
# 使用sort()方法
my_list = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]
my_list.sort()
print(my_list)  # 输出：[1, 1, 2, 3, 3, 4, 5, 5, 5, 6, 9]
# 原列表已经被修改

# 使用sorted()函数
my_list = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]
sorted_list = sorted(my_list)
print(sorted_list)  # 输出：[1, 1, 2, 3, 3, 4, 5, 5, 5, 6, 9]
print(my_list)  # 输出：[3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]
# 原列表没有被修改，sorted()函数返回了一个新的已排序列表

```

因此，如果你想要在不修改原始列表的情况下对列表进行排序，你应该使用 `sorted()` 函数；如果你想在原地修改列表并且不需要返回值，你应该使用 `sort()` 方法。