---
Date: 2024-02-07
tags:
  - Python
---
## If j in ( 0, i )
`j` 是否属于元组 `(0, i)`。具体来说，它会返回一个布尔值，表示 `j` 是否等于 0 或者等于 `i`。
如果 `j` 的值等于 0 或者等于 `i`，则表达式的结果为 `True`；否则，结果为 `False`。

可參考[[118. Pascal's Triangle 帕斯卡三角形🟢]]的Leetcode題目用法

## if haystack[i: i+len(needle)] == needle:
一次就可以確認下標 i + 要確認的是否有相同的字串的長度，是否跟needle相同。

可參考 [[28. Find the Index of the First Occurrence in a String 尋找字串中第一次出現的索引🟢]] 的Leetcode 題目用法。

## lambda 表達式：
通常是在需要一個函數，但是又不想費神去命名一個函數的場合下使用，也就是指匿名函數

```python
max = lambda m, n: m if m > n else n
print(max(10, 3))  # 顯示 10
```
## enumerate()
enumerate() 函數可以將一個可迭代的對象（如列表、元組或字串）轉換為索引序列，同時列出數據和數據對應的索引值。它可以讓你在遍歷列表的同時擁有索引值，並且不用使用內建的 range() 函數。