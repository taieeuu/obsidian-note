---
Date: 2024-02-15
relation:
---
# 評價
* 面試感受 : ★★★
* 難度 : ★★★
* 想去程度 : ★★★
# 面試題目
## tuple、list、dict，在python中差異
- **Tuple(元組) :**
	- 定義 : 
		用小括號 ' () ' 定義，例: my_tuple = ( 1, 2, 3 )
	- 不可變性 : 
		元組是不可變的，一旦創建就不能修改。不能做添加、刪除等動作。
	- 索引：
		元組中的元素可以透過索引來訪問，索引從0開始。
- **List（列表）:**
	- 定義：
		以方括號 [] 定義，例如 my_list = [1, 2, 3]。
	- 可變性：
		清單是可變的，可以透過索引進行修改、新增或刪除元素。
	- 索引：
		清單中的元素也可以透過索引來訪問，索引從0開始。
- **Dictionary（字典）:**
	- 定義：用花括號 {} 定義，例如 my_dict = {'a': 1, 'b': 2, 'c': 3}。
	- 鍵值對：字典是由鍵值對組成的，每個鍵與其對應的值之間以冒號分隔。
	- 可變性：字典是可變的，可以透過鍵來新增、刪除或修改值。
	- 無序性：字典中的鍵值對是無序的，即字典中元素的順序不一定與定義時的順序相同。
## Tuple跟set()差異
- **Tuple（元組）:**
	- 定義：以小括號 () 定義，例如 my_tuple = (1, 2, 3)。
	- 不可變性：元組是不可變的，一旦創建就不能修改。 不能新增、刪除或修改元組的元素。
	- 有序性：元組中的元素是有序的，可以透過索引來訪問，索引從0開始。
- **Set（集合）:**
	- 定義：用花括號 {} 或 set() 函數定義，例如 my_set = {1, 2, 3} 或 my_set = set([1, 2, 3])。
	- 可變性：集合是可變的，可以透過新增或刪除元素來修改集合。
	- 無序性：集合中的元素是無序的，不能透過索引來存取。
## Django Request到response流程
![[凱文科技 - {RD}Python工程師 Python Engineer 1.png|307]]

![[凱文科技 - {RD}Python工程師 Python Engineer 2.png|350]]

1. 用戶瀏覽器發起http請求；
2. 請求先到達 Request Middleware 中間件，它能在views 收到請求前對Request訊息內容進行處理，發送回應；
3. urls.py 中的 URLConf 對收到請求的url進行匹配，找到相應的視圖處理函數或視圖類別；
4. 在View收到請求之前，View Middlewares 被調用， 可以進行一些預處理；
5. 視圖函數或視圖類別被呼叫；
6. 視圖函數可能存取模型數據，也可能呼叫 form 生成表單物件；
7. 所有 model-to-DB 的互動都透過 manager 物件來完成；
8. Views 可通以透過context 物件來新增更多傳給範本的內容；
9. Views 將 context 上下文資料傳給範本進行渲染；
　　模板層使用 Filter與 Tags 渲染輸出；
　　範本圖層將渲染結果 傳回給 視圖圖層；
　　視圖層發送HTTPResponse 給Response Middlerwares 中介軟體；
　　Response middlewares中間件可以為Response 訊息新增額外內容，或以1個新訊息取代 wsgi 閘道傳送 response 給使用者瀏覽器；

詳細可參考: https://www.cnblogs.com/cxq1126/p/17525879.html
## 淺層copy跟深層copy差異
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
## MTV跟MVC架構
**MVC**
![[凱文科技 - {RD}Python工程師 Python Engineer 3.png|425]]
M （Model，模型）： 封裝與資料庫的訪問，處理對資料庫的增刪改查（CRUD）。
V （View，視圖）：負責（1）處理業務邏輯、封裝結果；（2）生成頁面展現給使用者。
C （Controller，控制器）：對 Request、Response 進行處理、處理 Model 與 View 的交互。

**MTV**
![[凱文科技 - {RD}Python工程師 Python Engineer 4.png|425]]
M （Model，模型）： 同上 Model。
T （Template，模板）：同上的 View （2）生成頁面展現給使用者的部分，也就是我們看見的前端 HTML、CSS、JavaScript 的部分。
V （Views，視圖）：同上的 View （1）處理業務邏輯、封裝結果的部分，負責處理 URL 與 callback 函式之間的關係，每一個view都代表一個簡單的Python function。
C （Controller，控制器）的部分如果要對應的話為 Django 本身。
## Restful API 
### 為何不是使用Get反而是用Post
通常使用POST請求比GET請求更安全。 當使用者提交使用者名稱和密碼等敏感資訊時，使用POST請求可以將這些資訊作為請求體的一部分發送到伺服器，而不是像GET請求那樣將其附加在URL中。 這樣可以避免在URL中留下敏感訊息，因為URL可能會被瀏覽器、代理伺服器或日誌記錄系統記錄，從而增加資訊外洩的風險。

使用POST請求的主要**優點**包括：

安全性更高： 敏感資訊不會以明文形式出現在URL中，減少了資訊外洩的風險。
請求體： 可以將使用者提交的資料放在請求體中，這對於傳輸大量資料或檔案更為方便。
不可書籤化： POST請求不會被瀏覽器保存在瀏覽記錄中，因此不容易被使用者直接書籤或分享。
##  Django Setting 中的 Debug=True or False差異
當DEBUG = True時的好處為

1. 如果開啟了DEBUG模式，那麼以後我們修改了Django項目的程式碼，然後按下ctrl+s，那麼Django就會自動的給我們重啟項目，不需要手動重啟。
2. 如果開啟了DEBUG模式，那麼以後Django專案中的程式碼出現bug了，那麼在瀏覽器中和控制台會列印出錯訊息。 否則的我們很難找到bug的位置，也不方便調試程式碼。

在生產環境中，禁止開啟DEBUG = True，因為當你的網站出錯誤時，別人能看到你的原始碼，而我們也不需要給使用者看到這些錯誤訊息。 所以需要關掉DEBUG = True，也就是設定DEBUG = False

如果設定了DEBUG = False，那麼就必須設定settings.py中的ALLOWED_HOSTS.

ALLOWED_HOSTS：這個變數是用來設定以後別人只能透過這個變數中的ip位址或網域來存取。
## Docker container 跟 image 差異，解釋
**Docker鏡像（鏡像檔）**
唯一的環境模板，介紹Container內的所有程式（包括應用程式、函式庫、環境設定檔），用於建立Docker容器
**Docker容器（container）**
容器是根據docker映像建立的實例。鏡像是唯一的，而容器是可寫入層。每個容器之間都是相互獨立的平台。容器是一個簡單版本的  。
## 專案使用Django的哪個
## Git Flow
Git Flow 的建議，主要的分支有 `master`、`develop`、`hotfix`、`release` 以及 `feature` 這五種分支，各種分支負責不同的功能。其中 Master 以及 Develop 這兩個分支又被稱做長期分支，因為他們會一直存活在整個 Git Flow 裡，而其它的分支大多會因任務結束而被刪除。

![[凱文科技 - {RD}Python工程師 Python Engineer 5.png]]

**Master 分支**

主要是用來放穩定、隨時可上線的版本。這個分支的來源只能從別的分支合併過來，開發者不會直接 Commit 到這個分支。因為是穩定版本，所以通常也會在這個分支上的 Commit 上打上版本號標籤。


**Develop 分支**

這個分支主要是所有開發的基礎分支，當要新增功能的時候，所有的 Feature 分支都是從這個分支切出去的。而 Feature 分支的功能完成後，也都會合併回來這個分支。

**Hotfix 分支**

當線上產品發生緊急問題的時候，會從 Master 分支開一個 Hotfix 分支出來進行修復，Hotfix 分支修復完成之後，會合併回 Master 分支，也同時會合併一份到 Develop 分支。

為什麼要合併回 Develop 分支？如果不這麼做，等到時候 Develop 分支完成並且合併回 Master 分支的時候，那個問題就又再次出現了。

那為什麼一開始不從 Develop 分支切出來修？因為 Develop 分支的功能可能尚在開發中，這時候硬是要從這裡切出去修再合併回 Master 分支，只會造成更大的災難。

**Release 分支**

當認為 Develop 分支夠成熟了，就可以把 Develop 分支合併到 Release 分支，在這邊進行算是上線前的最後測試。測試完成後，Release 分支將會同時合併到 Master 以及 Develop 這兩個分支上。Master 分支是上線版本，而合併回 Develop 分支的目的，是因為可能在 Release 分支上還會測到並修正一些問題，所以需要跟 Develop 分支同步，免得之後的版本又再度出現同樣的問題。

**Feature 分支**

當要開始新增功能的時候，就是使用 Feature 分支的時候了。Feature 分支都是從 Develop 分支來的，完成之後會再併回 Develop 分支。
## 資料庫正規化
資料庫正規化是一種設計和組織關聯式資料庫的過程，目的是最大程度地減少資料冗余，提高資料庫的效能和靈活性。這個過程通常包括將資料表分解成更小的、相關的表，以減少數據重複存儲，同時確保資料的一致性和完整性。

資料庫正規化通常分為不同的正規化級別，通常從第一正規化（1NF）到第五正規化（5NF）。每個正規化級別都有特定的規則和條件，確保資料庫結構的最佳化。

主要的正規化原則包括：
1. **第一正規化（1NF）：** 確保每個資料表中的每個欄位都是不可再分的原子值，不包含重複的群組或陣列。 
2. **第二正規化（2NF）：** 在1NF的基礎上，確保每個非主鍵欄位都完全依賴於整個主鍵，而不是僅依賴於主鍵的一部分。
3. **第三正規化（3NF）：** 在2NF的基礎上，確保每個非主鍵欄位都不依賴於其他非主鍵欄位。
4. **BCNF（Boyce-Codd正規化）：** 這是一種更進一步的正規化，確保每個非主鍵欄位都完全依賴於每個超鍵，而不僅僅是主鍵。
5. **第四正規化（4NF）和第五正規化（5NF）：** 這些正規化級別處理多值依賴性和聯合依賴性的情況，進一步細化資料結構。 正規化的目標是減少冗余數據，確保數據一致性，提高資料庫的靈活性和效能。然而，在某些情況下，過度正規化也可能導致複雜的查詢和效能下降，因此需要在正規化和性能之間取得平衡。

**詳細可參考:** http://cc.cust.edu.tw/~ccchen/doc/db_04.pdf
# 程式觀念題
## 下列輸出
沒有顯示任何東西，因為這邊的 D 類別中有 test 方法，使得Python不會去辨識基類是否有 test 方法。

```python 
class A:
    def test(self):
        print("I am A")

class B:
    def test(self):
        pass

class C(A, B):
    def test(self):
        print("I am C")

class D(A, B):
    def __init__(self) -> None:
        super().__init__()

    def test(self):
        pass

d = D()
d.test()

```
## @property意思
```python
class A():

	@property
	def sc(self):
		print("inside the getscore")
a = A()
print(a.sc())

class square():
	def __init__(self. sideLen):
		self.__sideLen = sideLen
	@property
	def area(self):
		return self.__sideLen ** 2
obj = Square(10)
print(obj.area)
```

**Ans :**
`@property`：這是Python中的一個裝飾器，用來定義屬性的getter方法`area`。該`area`屬性提供了一種訪問正方形區域的方法，無需像方法那樣調用它，而是像屬性一樣調用它。==不用寫像print(obj.area())這樣去訪問==。

**好處**
1. 封裝和資料隱藏性: 封裝和資料隱藏：透過使用雙下劃線（`__sideLen`），你可以建立`sideLen`一個私有屬性，並且不能從類別外部直接存取它。這有助於封裝物件的內部狀態並促進資料隱藏，這是物件導向程式設計的基本原則。
    
2. 可讀性和簡單性：裝飾器允許您使用語法而不是 來`@property`存取正方形的區域。這可以產生更具可讀性和自然性的程式碼，使其更易於理解和維護。`obj.area``obj.area()`
    
3. 一致性：透過將區域公開為屬性，您可以提供用於存取計算值的一致介面。類別的使用者可以像對待任何其他屬性一樣對待它，從而減少記住是否在方法呼叫中使用括號的需要。
    
4. 動態計算：此`area`屬性根據邊長的當前值動態計算面積。如果邊長在建立物件後發生變化，則存取該`area`屬性將始終傳回最新值，而無需明確呼叫方法。
# 心得
我認為這家技術面試題目都屬於滿簡單的，但第一次面試正職的工作，回答得有點糟糕，一些基本的題目都忘記，沒有回答好。有點想懊惱，哀 !