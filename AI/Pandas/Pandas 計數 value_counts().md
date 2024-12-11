---
Date: 2024-02-25
tags:
  - Python
  - 方法
---
# 描述
value_counts()函数可以对Series里面的每个值进行计数并且排序。
# 語法
# 實例
DataFrame，如下
![[Pandas 計數 value_counts() 1.png|234]]

每個區域出現了多少次，可以簡單如下：

![[Pandas 計數 value_counts() 2.png|243]]

每個區域都被計數，並且預設從最高到最低做降序排列。

如果想用升序排列，可以加參數ascending=True：

![[Pasted image 20240225200640.png|351]]

如果想得出的计数占比，可以加参数normalize=True：

![[Pasted image 20240225200659.png|341]]