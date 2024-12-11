---
Date: 2024-02-19
tags: 
relation:
---
沒有順序、不可重複的元素集合

無法像list或tuple去取特定值

運算：聯集(|) 、交集(&)、差集(-)、排除交集(^)

```python
V = {'a', 'e', 'u', 'i', 'o'}  
F = {'d', 'n', 'g', 'i', 'o'}  
V|F >>{'a', 'e', 'u', 'i', 'o','d', 'n', 'g'}  
V&F >>{'i', 'o'}  
V-F >>{'a', 'e', 'u'} (V扣掉F內的元素)  
V^F >>{'a', 'e', 'u', 'd', 'n', 'g'}
```

常用函式: set.add(新增單個元素) set.update(新增多個元素)

set.remove(移除不存在的元素會出錯)、set.discard(移除不存在的元素不會出錯)

元素 in dict >> 元素是否在dict中