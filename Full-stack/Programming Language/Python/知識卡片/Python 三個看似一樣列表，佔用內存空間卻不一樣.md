---
Date: 
tags: 
relation:
---
# 前言
Python是一個腳本語言，是由C編寫成的虛擬機上，會將我們寫好的Python先轉換成字節碼，然後再用C語言編成的虛擬機運行這些字節碼

查看Python代碼之間差異時，我們會先使用dis這個module來編譯成字節碼
```python
import sys
import dis

print(sys.getsizeof([0]*3))
print(sys.getsizeof([0 ,0, 0]))
print(sys.getsizeof([0 for _ in range(3)]))

  
print(dis.dis("[0] * 3"))
print('-'*10)
print(dis.dis("[0,0,0]"))
print('-'*10)
print(dis.dis("[0 for _ in range(3)]"))
```

![[Python 三個看似一樣列表，佔用內存空間卻不一樣 1.png]]
>[!info]
>每個所實現的方式都不相同，分別為binary_mutiply、tupe再list_extend、list_append

## [0] * 3
首先來看內存最小的`[0]*3`，使用cpytho實現的代碼在listobject.c裡面。
![[Python 三個看似一樣列表，佔用內存空間卻不一樣 2.png|425]]
>[!info]
>multiply到最後，實際上用到list_repeat，傳進來的參數為一個list和一個n，那這邊在申請內存為552行的list_new_prealloc

![[Pasted image 20240429211058.png|434]]
>[!info]
>list_new_prealloc在項操作系統申請內存時PyMem_new()，也就是他需要多少size就申請多少的size，然後這邊的size，是list的size乘上一個n => `1 * 3 = 3。`
>在python 3.9中，一個空的list占用56個byte，我是64位元作業系統，每一個指針是8個byte，這裡面存了3個指針。=> 56 + 8 * 3 = 80

## [0,0,0]
**在測試下來發現內存變為88 (以後有用到去看)**
![[Python 三個看似一樣列表，佔用內存空間卻不一樣 3.png]]
>[!info]
>在 list_extend 函數裡面，像操作系統申請內存的部分在list_resize，他先看看這個list的size，然後再看看進來的list size，然後使用list_resize來改變自己allocate內存的量。
>這我們的情況m=0，然後後來需要的size 3，所以實際上運行list_resize(3)。

![[Python 三個看似一樣列表，佔用內存空間卻不一樣 4.png|500]]
>[!info]
>上述為append的實現函數app1()，那會發現app1實際上也是用了 list_resize 這個函數，來改變使用的內存空間大小

與剛剛的list_extend有什麼區別呢，剛剛的 list_extend 只使用一次的 list_resize(3)，而在append這裡我們可以看到，每一次會調用一個 list_resize(n+1)，從0開始就是 list_resize(1)，list_resize(2)...

**list_resize 解說**
可變長度，大家都可能會想成，我要1個我就跟作業系統要1個長度，2個則要2個，以此類推，但是如果只是簡單操作，會產生大量的內存碎片，而且要內存，也就是 c 裡面的malloc，這個操作是相對較慢的，因為它涉及sys call。
在絕大部分的語言，實現可變長度數組時，都是有一定的優化算法，一般來說是猜你需要用多少，先幫你備著。舉例: 假設我要5個內存，那會給你8個。這樣你要往裡面加時部會申請新的內存，填到8個時，然後再給你8個，或是4個看語言，
![[Python 三個看似一樣列表，佔用內存空間卻不一樣 5.png]]
>[!info]
>這邊的self -> allocated ，就是我先在已經申請多少的內存，這些內存並不一定都使用了。而下面的54行 if 的部分，意思是說如果你需要新的size恰好在allocated和1/2 allocated之間，我就不給你申請新的內存，也不給你改變新的內存，你可以直接說我已經有這麼多的內存了。

## 重點代碼
![[Python 三個看似一樣列表，佔用內存空間卻不一樣 6.png|500]]
### 第一種情況
sys.getsizeof([0 ,0, 0])
extend直接 resize(3)，這裡的new_size是3，newsize>>3 => 0，3+6 =>9
然後這裡`~(size_t)3`特殊的操作是對4進行對齊，就是二禁制最後兩個bit不要了，9的話二進制是1001，也就會變成1000，也就是8。所以說當newsize直接是3時，而且我們需要resize時，這裡直接給他申請到了8內存，之前說了一個指針是8 byte，`8*8=64，64+56`是120個byte。
### list comprehension
這是使用list append，resize(1)，resize(2)....。假設resize(1)，這裡1，1>>3沒有了，1加6是7，7與4對齊=4，也就是說上來他給了他4塊內存。而resize(2)，resize(3)都會因為前面我們剛才介紹的**list_resize**函數，而直接改變size進行返回，不在重新分配配內存，因為每一次需求的size，都在allocated之內。

總結: 當resize(1)時，給了4個內存，resize(2) resize(3)，他只改變了size而沒有分配新的內存，每個指針是8格byte，`4*8=32`再加上56 = 88。