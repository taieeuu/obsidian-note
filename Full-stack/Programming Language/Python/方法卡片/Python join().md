---
Date: 2024-02-18
tags:
  - Python
relation:
---
# 描述
將序列中的元素以指定的字元連接產生一個新的字串。
# 語法
```python
<sep>.join(<iterable>)
```

- `<iterable>` 是任何包含子字符串的 Python 可迭代对象，例如一个列表或一个元组。
- `<sep>` 是你想要基于它加入子字符串的分隔符。
# 實例
```python
my_string = "Apples,Oranges,Pears,Bananas,Berries"

my_list = my_string.split(",")

# --result--
['Apples', 'Oranges', 'Pears', 'Bananas', 'Berries']
```
