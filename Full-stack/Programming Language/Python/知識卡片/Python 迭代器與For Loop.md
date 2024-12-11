---
Date: 2024-05-13
---
在 for loop 的實現背後，有兩個核心的概念，一、可迭代對象 (iterable)，二、迭代器 (iterator)。

在官網上面我們可以看到`可迭代對象 (iterable)`的定義，一個對象可以一個一個的返回他的成員，比如像是list、tuple、dictionary、string，也強調了iterable可以在 for loop in中實現，也就是for loop in 後面必須要是iterable

那 iterator是一個表示數據流的對象，你可以使用`__next__`函數，不斷地從這個對象裡面獲取新的數據。

這兩個我們會時常的搞混，我們從high level角度來看，iterable像是一個數據保存者，一個container，可無狀態，他可以完全不知道你的iterator數到哪裡，但她需要有能力產生一個iterator，而iterator一定是有狀態，但她不需要實現一個container，也就是說他知道它代表的iterable裡面是甚麼數據，但她不需要實現一個interface，來修改這個iterable裡面的數據。

一個iterable要馬有`__iter__`函數，要不然是一個sequence然後有`__getitem__`這個方法，這兩種都是為了要保證他，可以在`iter()`這個函數作用下，返回一個iterator，而iterator必須要有`__next__`這個函數，這個方法保證他在被`next()`作用時，可以返回下一個iterable裡面的值。
```python
import sys
import dis

lst = [1, 3, 5]
for x in it:
    print(x)
dis.dis("for x in it: print(x)")
```
![[Python 迭代器與For Loop 1.png]]
>[!info]
>這是for loop 的bytecode，我們可以看到在load_name後，他實現了一個get_iter的方式，而get_iter中的bytecode，從棧頂這個iterable裡面，拿出來所對應的iterator，雸所有的for loop都是這樣所實現的，所以我們可以理解為，當寫了一個for in 一個 iterable時，for loop 首先悄悄做了一個從iterable裡面拿到iterator的操作。

也就是你像下面這樣

```python
import sys
import dis

lst = [1, 3, 5]
it = iter(lst)

for x in it:
    print(x)
dis.dis("for x in it: print(x)")
```
>[!info]
>**注意**: 這邊的iter實際是在for loop執行前就做了這個，iterablec會先被取一個iter的值的這個動作，且for loop 是對這個 iter 來進行操作的，所以我們才不需要加 iter ，但我們還是要有這個概念。

乍看之下，理這些看似沒用，但有時候你會需要寫到iterable或是iterator的，舉一個簡單的例子，像是練表 linked list，在python 中視沒有這種數據結構，但在其實這種數據結構很常見。

如果我們寫在for loop 中使用鍊表的話，我們就需要自己把鍊表變成一個iterable。
```python
class NodeIter:
    def __init__(self, node):
        self.curr_node = node
    def __next__(self):
        if self.curr_node is None:
            raise StopIteration
        node, self.curr_node = self.curr_node, self.curr_node.next
        return node
        
class Node:
    def __init__(self, name):
        self.name = name
        self.next = None

    def __iter__(self):
        return NodeIter(self)

node1 = Node("node1")
node2 = Node("node2")
node3 = Node("node3")
node1.next = node2
node2.next = node3

for node in node1:
    print(node.name)  # 這裡直接打印節點的名稱
```
>[!info]
>這裡有一個iterator class，裡面寫了一個iterator class就必須實現的`__next__`函數，每次調用`__next__`函數時，需要在兩件事中選擇一個。一、如果你的iterator已經到了盡頭，沒有辦法再給出數據，就需要**raise一個StopIteration**這個exception，二、如果還有數據的話，把這個數據 return 回去。
>在這個實現中，使用了 self.curr_node 表示現在已經屬到哪一個 node 了，如果是None的話，就說明已經沒有了，就raise，否則就往前進一個，把現在這個 return 回去。
>那Node class的話，就是一個 iterable 他實現了這個 `__iter__`函數，返回 Nodelter 的一個 object。
>這裡也有測試簡單的測試，建立三個 node 然後 node1 下面是 node2 、node3，然後使用了`for node in node1`因為這邊每個 node 都是iterable，node1為表頭指向下一個node2再下一個node3。

我們會發現這個 iterator 除了`__next__`之外，還需要時現`__iter__`函數，會什麼這邊沒有實現呢，官方的文檔中也明確的說明 iterator 必須要實現 `__iter__`函數，也說到**這麼做是要確保每一個iterator也是iterable**，就是要讓每一個迭代器也是可迭代的。

上述的代碼是先跟 node class 要了一個 iterator ，下面代碼為常出現的寫法
```python
node1 = Node("node1")
node2 = Node("node2")
node3 = Node("node3")
node1.next = node2
node2.next = node3

it = iter(node1)
first = next(it)

for node in it:
    print(node.name)
```

這裡想第一個 node 不要從第二個開始打印，當然也可以通過 for loop 實現，這邊先拿到了 node1 的iterator，然後從裡面拿到了一個值，再對這個 iterator 做一個 for loop，看起來相當合理，但沒想到會抱錯，那是因為在我們做 `for node in it` 時，這個 it 本身雖然是個 iterator 但她不是一個 iterable，所以他不能放到 for loop in 裡面。

因此 python官方規定 iterator，本身必須要是 iterable 這樣放進 for loop裡面或是放到一些其他需要 iterable 像是 zip() 、map() 這些函數中才能使用，那以下是實現方式。

```python
class NodeIter:
    def __init__(self, node):
        self.curr_node = node
    def __next__(self):
        if self.curr_node is None:
            raise StopIteration
        node, self.curr_node = self.curr_node, self.curr_node.next
        return node
    def __iter__(self):
	    return self
```
> [!info]
> 對於 iterator本身來說，幾乎所有情況，只需要定義一個 `__iter__`函數，返回 self，就是當你對這個 iterator 求他的 iterator 時候，也就是他自己。打印一下會發現結果已經一樣了。

而你還會發現文檔中還說 cpython 本身有時也不總是遵循每一個 iterator 都是  iterable ，**所以我們還是在定義時就添加上去，更重要是我們要知道這樣的寫法有什麼樣的區別以及意義，也就是什麼是 iterator 、什麼是 iterable。** 如果能理解這兩個有甚麼區別，那誰要寫`__next__`以及誰要寫`__iter__`就很清楚了。

參考: https://www.youtube.com/watch?v=vfQdRp_PhhQ