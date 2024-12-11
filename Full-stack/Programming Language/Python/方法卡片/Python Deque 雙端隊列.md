---
Date: 2024-04-07
tags:
  - Python
relation:
---
# 描述
Python標準庫collections 中的一個類，是double-ended queue的縮寫，實現了兩端都可以操作的隊列，相當於雙端隊列。類似list，與list不同的是，deque實作擁有更低的時間和空間複雜度。
# 實例
```python 
from collections import deque

people = ['Mario', 'Luigi', 'Toad']
queue = deque(people)

queue.append('Bowser')
print(queue)

queue.popleft()
print(queue)

queue.appendleft('Daisy')
print(queue)

queue.retate()
print(queue)

queue.extend(['Shy Guy', 'Yoshi'])
print(queue)


queue.reverse()
print(queue)

#----result----
queue(['Mario', 'Luigi', 'Toad', 'Bowser'])
queue(['Luigi', 'Toad', 'Bowser'])
queue(['Daisy', 'Luigi', 'Toad', 'Bowser'])
queue(['Luigi', 'Toad', 'Bowser', 'Daisy'])
queue(['Luigi', 'Toad', 'Bowser', 'Daisy', 'Shy Guy', 'Yoshi'])
queue(['Yoshi', 'Shy Guy', 'Daisy' 'Bowser', 'Toad', 'Luigi'])
```
# 注意
使用deque無法像list那樣使用slice
```python
dq[1000:1500]

#----result----
TypeError             Traceback (most recent call last)
<ipython-input-107-df775f3348c6> in <module>()
----> 1 dq[1000:1500]

TypeError: sequence index must be integer, not 'slice'
```
需使用
```python
import itertools

dq_slice = itertools.islice(dq, 1000, 1500)
```
