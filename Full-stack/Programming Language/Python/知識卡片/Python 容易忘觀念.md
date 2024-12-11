---
Date: 2024-02-15
tags:
  - Python
---
## Deep Copy and Shallow Copy
**淺複製 與 深複製 的差別**  
可變型別有：列表，字典  
不可變型別有：數字，字串，元組

為了循序漸進，這裡分兩個例子講解
```python
a = [1, [2, 3]]
a_equal = a
a_shallowcopy = copy.copy(a)
a_deepcopy = copy.deepcopy(a)
a[0] = 4    # 改變a中的第一個元素，即改變a中的數字
print('a:               ', a,       'id(a):             ', id(a))
print('a_equal:         ', a_equal, 'id(a_equal):       ', id(a_equal))
print('a_shallowcopy:   ', a_shallowcopy, 'id(a_shallowcopy): ', id(a_shallowcopy))
print('a_deepcopy:      ', a_deepcopy, 'id(a_deepcopy):    ', id(a_deepcopy))
```
*輸出*
```
a: [4, [2, 3]] id(a): 4578314360  
a_equal: [4, [2, 3]] id(a_equal): 4578314360  
a_shallowcopy: [1, [2, 3]] id(a_shallowcopy): 4562436248  
a_deepcopy: [1, [2, 3]] id(a_deepcopy): 4562362096
```

**可以發現**
=賦值： 對於修改，受到影響，地址相同  
淺複製：對於修改，不受影響，地址不相同  
深複製：對於修改，不受影響，地址不相同

這麼一看，深複製和淺複製完全一樣啊，哪裡有區別？
還記得上面說的可變型別嗎？ 剛一看剛剛改變list a當中的第一個元素，是一個數字吧。  
數字是不可變型別，所以我們在修改不可變型別時，深複製和淺複製沒有區別。  
那麼當我們改變a當中第二個元素，即list呢，會怎麼樣，請看例子2:

```python
a = [1, [2, 3]]
a_equal = a
a_shallowcopy = copy.copy(a)
a_deepcopy = copy.deepcopy(a)
a[0] = 4    # 改變a中的第一個元素，即改變a中的數字
a[1][1] = 5     # 改變a當中第二個元素中的元素，即 改變a當中的list
print('a:               ', a,       'id(a):             ', id(a))
print('a_equal:         ', a_equal, 'id(a_equal):       ', id(a_equal))
print('a_shallowcopy:   ', a_shallowcopy, 'id(a_shallowcopy): ', id(a_shallowcopy))
print('a_deepcopy:      ', a_deepcopy, 'id(a_deepcopy):    ', id(a_deepcopy))
```
*輸出*
```
a: [4, [2, 5]] id(a): 4454672504  
a_equal: [4, [2, 5]] id(a_equal): 4454672504  
a_shallowcopy: [1, [2, 5]] id(a_shallowcopy): 4438794392  
a_deepcopy: [1, [2, 3]] id(a_deepcopy): 4438720240
```
**可以發現：**  
=賦值： 對於修改，受到影響，地址相同  
淺複製：對於不變型別的修改，不受影響；對於可變型別的修改，受影響，地址不相同  
深複製：對於不變型別的修改，不受影響；對於可變型別的修改，不受影響，地址不相同
#### 總結
為了不必要的麻煩，我們想要一個完全獨立的變數，要一個與原始變數解耦的變數，那請使用深複製吧。
還可以從另外一個角度理解深複製和淺複製的區別，那麼就是  
淺複製僅複製父物件，不會複製物件內部的子物件(如例子2當中的a的第二個元素[2,3])  
深複製不僅複製父物件，而且還複製內部的子物件。
