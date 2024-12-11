---
Date: 2024-08-18
---
繼承上一篇[[Django 數據表操作]]，這一篇延續講述數據查詢、執行SQL語句等操作。
### 數據查詢
修改數據，往往只修改某行數據內容，因此再修改數據之前還要對模型進行查詢操作，確定數據表某行數據對象，最後才執行數據修改操作
#### 全部查詢
`all()` 方法用於查詢數據表中的所有記錄。

```python
# 查詢 Book 表中的所有記錄
# Selct * from Book 數據已列表返回
books = Book.objects.all()
```

這將返回一個 `QuerySet`，包含表中的所有書籍記錄。
#### 條件查詢
使用 `filter()` 方法可以根據特定條件篩選數據。這是最常用的查詢方式之一。SQL 的 `and` 查詢基本都在 `filter` 中添加多個查詢條件。

返回一個 `QuerySet`，這個 `QuerySet` 是一個包含零條、單條或多條記錄的集合，即使只有一條記錄，它也會以 `QuerySet` 的形式返回。

```python
# 查詢所有作者為 "J.K. Rowling" 的書籍
books_by_rowling = Book.objects.filter(author="J.K. Rowling")

# books 是一個 QuerySet，即使查詢結果只有一條記錄

# filter 的查詢條件可設為字典格式
d = dict(author='J.K. Rowling', id=3)
v = Model.objects.filter(**d)
```

- `filter()` 返回一個 `QuerySet`，包含符合條件的所有記錄。
#### 單條記錄查詢
使用 `get()` 方法可以查詢單一的記錄。這個方法需要保證查詢結果只有一條，否則會拋出異常。

返回單個模型實例。這個方法假設查詢結果中應該只有一條記錄，並直接返回該記錄的實例。

```python
# 查詢 id 為 1 的書籍
# select * from Book where id=1
book = Book.objects.get(id=1)

# book 是一個 Book 模型的實例
```

- 如果找到多於一條記錄，會拋出 `MultipleObjectsReturned` 異常。
- 如果找不到記錄，會拋出 `DoesNotExist` 異常。
#### 排序查詢
使用 `order_by()` 方法可以對查詢結果進行排序。

```python
# 按價格升序排序查詢結果
books = Book.objects.all().order_by('price')

# 按價格降序排序
books = Book.objects.all().order_by('-price')
```

- 字段名前加 `-` 表示降序排序，不加則表示升序排序。
#### 限制查詢結果
使用 `[:n]` 可以限制查詢結果的數量，類似於 SQL 中的 `LIMIT` 語句。

```python
# 查詢前 5 本書
# Select * from Book LIMIT 5
books = Book.objects.all()[:5]
```
#### 字段篩選
使用 `values()` 或 `values_list()` 方法，可以只查詢指定的字段，而不是整個記錄。

```python
# 只查詢書名和作者
# Select title, author from Book
books = Book.objects.values('title', 'author')

# 只查詢書名列表
book_titles = Book.objects.values_list('title', flat=True)
```
#### 去重查詢
使用 `distinct()` 方法可以過濾查詢結果中的重複值。

```python
# 查詢所有不同的作者
distinct_authors = Book.objects.values('author').distinct()
```
#### 複合查詢
SQL 的 `or` 查詢，需要引入 `Q`，編寫格式: `Q(field=value) | Q(field=value)`，多個 `Q` 之間可以使用 `|` 進行隔開。

也可以使用 `Q` 對象進行複雜的查詢，如 OR 或 AND 條件。

```python
from django.db.models import Q

# 查詢作者為 "J.K. Rowling" 或 "George Orwell" 的書籍
# Select * from Book where author='J.K. Rowling' or author="George Orwell"
books = Book.objects.filter(Q(author="J.K. Rowling") | Q(author="George Orwell"))
```
#### 不等於查詢
進行不等於條件的查詢可以使用 `exclude()` 方法或者 `filter()` 方法與 `__ne` 或 `~Q` 結合來實現。以下是一些常見的用法示例：
##### 使用 `exclude()` 方法
```python
# 查詢所有作者不是 "J.K. Rowling" 的書籍 
books = Book.objects.exclude(author="J.K. Rowling")
```

這個查詢會返回所有 `author` 不等於 `"J.K. Rowling"` 的書籍記錄。
##### 使用 `filter()` 與 `Q` 對象結合
可以使用 `Q` 對象與 `~`（位元運算符 NOT）結合來進行不等於查詢。

```python
# 查詢所有作者不是 "J.K. Rowling" 的書籍 
books = Book.objects.filter(~Q(author="J.K. Rowling"))
```

這樣也可以實現和 `exclude()` 相同的效果。
##### 使用 `filter()` 與 `__ne` 查詢
在 Django ORM 中，默認不支持 `__ne` 作為查詢條件，但可以通過 `exclude()` 或 `~Q` 來實現類似效果。`__ne` 的概念來自其他 ORM 或查詢語言，但在 Django 中不直接使用。
#### 聚合查詢
Django 提供了聚合函數來進行數據的統計計算，如計數、平均值、總和等。

```python
from django.db.models import Count, Avg, Sum

# 計算書籍總數
total_books = Book.objects.count()

# 計算書籍價格的平均值
average_price = Book.objects.aggregate(Avg('price'))

# 計算書籍價格的總和
total_price = Book.objects.aggregate(Sum('price'))
```
#### 原生 SQL 查詢
有時需要執行原生 SQL 查詢，Django 提供了 `raw()` 方法來支持這樣的操作。

```python
# 使用原生 SQL 查詢書籍
books = Book.objects.raw('SELECT * FROM myapp_book WHERE author = %s', ['J.K. Rowling'])
```
#### 延遲查詢
Django 的 `QuerySet` 是延遲執行的，這意味著查詢不會立即發生，只有在真正使用查詢結果時（如遍歷或強制轉換為列表）才會執行 SQL 查詢。

```python
# 此時不會執行查詢
books = Book.objects.filter(author="J.K. Rowling")

# 當我們遍歷 QuerySet 時，才會執行查詢
for book in books:
    print(book.title)
```
#### 數量查詢
數量查詢用於計算數據庫中符合特定條件的記錄數量。Django 提供了兩種主要方法來完成數量查詢：

```python
# 查詢 Book 表中的總數
total_books = Book.objects.count()

# 查詢作者為 "J.K. Rowling" 的書籍數量
rowling_books_count = Book.objects.filter(author="J.K. Rowling").count()
```

**`aggregate()`**：可以用來進行更為複雜的聚合操作，例如計算數量、平均值、總和等。如上面有介紹般。
#### 去重查詢
去重查詢用於過濾查詢結果中的重複值，通常使用 `distinct()` 方法來實現。

**`distinct()`**：去除查詢結果中指定字段的重複值。

```python
# 查詢所有不同的作者
# select DISTINCT author from Book
distinct_authors = Book.objects.values('author').distinct()

# 查詢所有不同的書名和作者組合
distinct_books = Book.objects.values('title', 'author').distinct()

# 查詢作者=JK，且去重操作
# select DISTINCT author from Book where author=JK
distinct_authors = Book.objects.filter(author='JK').distinct()
```

需要注意的是，`distinct()` 只會去除完全相同的記錄，如果僅需要某個字段去重，則需要使用 `values()` 或 `values_list()` 來配合。
#### 排序查詢
排序查詢用於將查詢結果按指定的字段進行升序或降序排列。Django 的 `order_by()` 方法用於實現這一功能。

**`order_by()`**：對查詢結果按指定字段排序。

```python
# 按價格升序排序書籍
books = Book.objects.order_by('price')

# 按價格降序排序書籍
books = Book.objects.order_by('-price')

# 同時按多個字段排序，先按作者升序，再按價格降序
books = Book.objects.order_by('author', '-price')
```

默認情況下，`order_by()` 按升序排序。如果需要降序排列，可以在字段名前加上 `-` 符號。

---
上述查詢講述了大部分的數據查詢方法，但有時在開發時會需要設置不同查詢條件來滿足多方的查詢需求。我們可以使用 **匹配符**。

[官方 QuerySet API reference](https://docs.djangoproject.com/en/5.1/ref/models/querysets/#queryset-api-reference)
#### 篩選範圍內的數據
當你需要篩選出字段值在某個範圍內的數據時，會使用 `__gt`（大於）、`__lt`（小於）、`__gte`（大於或等於）和 `__lte`（小於或等於）這些符號。例如：
###### 篩選價格在某範圍內的商品
```python
# 查詢價格在 20 到 50 之間的書籍
books = Book.objects.filter(price__gte=20, price__lte=50)
```
###### 篩選某個日期之前或之後的事件
```python
from datetime import date

# 查詢日期在今天之後的事件
upcoming_events = Event.objects.filter(event_date__gt=date.today())
```
#### 模糊查詢
當你需要查找字段中包含、以某個值開頭或結尾的數據時，會使用模糊查詢符號，如 `__contains`, `__icontains`, `__startswith`, `__istartswith`, `__endswith`, `__iendswith`。這在文本查詢和字符串匹配場景中特別有用。
###### 查找包含特定子字符串的記錄
```python
# 查詢書名中包含 "Python" 的書籍
books = Book.objects.filter(title__icontains="Python")
```
###### 查找以某個特定前綴開頭的數據
```python
# 查詢用戶名以 "admin" 開頭的用戶
admins = User.objects.filter(username__startswith="admin")
```
###### 查找以某個特定後綴結尾的數據
```python
# 查詢郵箱地址以 "@gmail.com" 結尾的用戶
gmail_users = User.objects.filter(email__iendswith="@gmail.com")
```
#### 處理日期和時間範圍
當你需要篩選特定時間範圍內的記錄時，`__gt`, `__lt`, `__gte`, `__lte` 這些符號非常有用。例如：
###### 篩選某個日期之後的訂單
```python
from datetime import datetime

# 查詢 2023 年 1 月 1 日之後的訂單
recent_orders = Order.objects.filter(order_date__gt=datetime(2023, 1, 1))
```
###### 篩選特定月份或年份的記錄
```python
# 查詢 2024 年 8 月的所有事件
events = Event.objects.filter(event_date__year=2024, event_date__month=8)
```
#### 對特定字段進行操作
有時你需要在查詢中比較一個字段與另一個字段的值，這時候 `F()` 物件和匹配符號一起使用，可以實現高效的字段間比較。
###### 比較數據庫中的字段
```python
from django.db.models import F

# 查詢價格比折扣更高的商品
expensive_products = Product.objects.filter(price__gt=F('discount'))
```
### 多表查詢
多表查詢的類型，可分為正向查詢、反向查詢。
#### 正向查詢
```python
# 查詢模型 Vocation 中的某行數據對象 v 
v = Vocation.objects.filter(id=1).first()
# v.name代表 外鍵 name
# 通過外鍵name去查詢模型 PersonInfo所對應的數據
v.name.hireDate
```
#### 反向查詢
```python
# 查詢模型 PersonInfo中的某行數據對象 p
p = PersonInfo.objects.filter(id=2).first()

# 方法一
# vocation_set的返回值為queryset對象，及查詢結果
# vocation_set的外鍵字段name不能設置參數related_name
# 模型vocation的外鍵字段name不能設置參數related_name
# 若設置參數related_name, 則無法使用vocated_set
v = p.vocation_set.first()
v.job

# 方法二
# 由模型Vocation的外鍵字段name的參數related_name實現
# 外鍵字段name必須設置參數related_name才有效，否則無法查詢
# 將外鍵字段name的參數related_name設為personinfo
v = p.perosninfo.first()
v.job
```

正向查詢和反向查詢還可以在查詢條件 (filter or get) 中使用。

無論是正向查詢還是反向查詢，它們在數據庫裡需要執行兩次SQL查詢，第一次查詢數據，在通過外鍵關聯獲取另一張數據表訊息。為了減少正向查詢和反向查詢中因外鍵關聯導致的多次 SQL 查詢，Django 提供了兩個強大的查詢優化方法：`select_related` 和 `prefetch_related`。這些方法可以顯著降低查詢次數，從而提高應用的性能。以下是優化查詢示例。
#### select_related
`select_related` 適用於外鍵（`ForeignKey`）和一對一關係（`OneToOneField`）。它會在執行查詢時使用 SQL 的 `JOIN` 操作，將相關聯的數據表一起查詢出來，從而減少查詢次數。
##### 示例：
假設我們有以下模型：

```python
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
```
###### 未優化的查詢
當我們查詢一本書並訪問其作者時，通常會進行兩次 SQL 查詢：

```python
# SELECT * FROM book WHERE id = 1;
book = Book.objects.get(id=1)
# SELECT * FROM author WHERE id = book.author_id;
author_name = book.author.name
```
###### 優化後的查詢：
使用 `select_related` 來優化查詢，只需一次查詢即可獲取書籍及其作者的信息，而這樣只會執行一次 SQL 查詢，通過 `JOIN` 一次性查出所需的數據：

```python
# SELECT * FROM book LEFT OUTER JOIN author ON book.author_id = author.id WHERE book.id = 1;
book = Book.objects.select_related('author').get(id=1)
author_name = book.author.name
```
#### prefetch_related
`prefetch_related` 適用於多對多關係（`ManyToManyField`）和反向外鍵查詢（多對一）。它會執行多次查詢，但通過 Python 層面的預取和關聯來減少數據庫查詢次數和複雜性。
##### 示例：
假設我們有以下模型：

```python
class Course(models.Model):
    name = models.CharField(max_length=100)

class Student(models.Model):
    name = models.CharField(max_length=100)
    courses = models.ManyToManyField(Course)
```
###### 未優化的查詢
當我們查詢一個學生及其選修的所有課程時，會進行多次 SQL 查詢（一次查學生，N 次查課程，N 為課程數量

```python
# SELECT * FROM student WHERE id = 1;
student = Student.objects.get(id=1)
# SELECT * FROM course INNER JOIN student_courses ON course.id = student_courses.course_id WHERE student_courses.student_id = 1;
courses = student.courses.all()
```
###### 優化後的查詢：
使用 `prefetch_related` 可以通過兩次查詢獲取所有數據（一次查學生，一次查課程），然後在 Python 層面進行關聯：\

```python
student = Student.objects.prefetch_related('courses').get(id=1)
courses = student.courses.all()
```
### 執行SQL語句
在 Django 中，有時你可能需要直接執行原生 SQL 語句，這通常是因為某些操作無法通過 Django ORM 來實現，或者你需要執行一些非常複雜和高效的查詢。在這些情況下，Django 提供了幾種方法來執行原生 SQL 語句：
#### extra
`extra` 適用於 ORM 難以實現的查詢條件，他將查詢條件使用原生 SQL 語法實現。此方法需要依靠模型對象，在某種程度上可以有效地防止SQL注入。

不過，從 Django 3.1 開始，`extra()` 已經被棄用，並且官方不再推薦使用它。
#### row
Django 的 `raw()` 方法允許你執行原生 SQL 查詢並返回模型實例的 `QuerySet`。這種方法適用於 `SELECT` 查詢。
###### 示例：
假設我們有一個 `Book` 模型，我們想要使用 SQL 查詢來檢索所有價格大於 20 的書籍：

```python
from myapp.models import Book

# 使用 raw() 方法執行原生 SQL 查詢
books = Book.objects.raw('SELECT * FROM myapp_book WHERE price > %s', [20])

for book in books:
    print(book.title)
```

- `raw()` 方法返回一個 `RawQuerySet`，它類似於普通的 `QuerySet`，但支持的是原生 SQL 查詢。
- 你可以通過 `%s` 作為佔位符來防止 SQL 注入，並在查詢中傳入參數。
#### execute() 和 executemany()
如果你需要執行批量插入或更新操作，可以使用 `execute()` 或 `executemany()` 來實現。
###### 批量插入示例：
```python
from django.db import connection

# 插入多條記錄
with connection.cursor() as cursor:
    cursor.executemany(
        'INSERT INTO myapp_book (title, price) VALUES (%s, %s)',
        [
            ('Book A', 30),
            ('Book B', 40),
            ('Book C', 50),
        ]
    )
```
#### connection.cursor()
當你需要執行非 `SELECT` 類型的 SQL 語句（例如 `INSERT`, `UPDATE`, `DELETE`）或者需要執行非常複雜的查詢時，可以使用 Django 提供的 `connection.cursor()` 方法。
###### 示例：
假設我們想要直接執行一個 SQL 語句來更新書籍的價格：

```python
from django.db import connection

# 使用 connection.cursor() 執行原生 SQL 語句
with connection.cursor() as cursor:
    cursor.execute('UPDATE myapp_book SET price = price + 10 WHERE price < %s', [20])
```

- `connection.cursor()` 方法允許你獲取一個數據庫游標，並使用標準的 SQL 查詢來操作數據庫。
- `cursor.execute()` 用於執行 SQL 語句，並且支持參數化查詢，這有助於防止 SQL 注入。
#### 使用 `transaction` 模塊
如果你的操作涉及多個 SQL 語句，並且你希望這些操作在同一個事務中完成，可以使用 Django 的 `transaction` 模塊來確保數據庫的原子性。

原子性 : 一個事務是一個不可分割的工作單位，事務中包括操作要馬都做，要馬都不做。
###### 示例：
```python
from django.db import connection, transaction

# 在一個事務中執行多個 SQL 語句
with transaction.atomic():
    with connection.cursor() as cursor:
        cursor.execute('UPDATE myapp_book SET price = price + 10 WHERE price < %s', [20])
        cursor.execute('DELETE FROM myapp_book WHERE price > %s', [100])
```

- 使用 `transaction.atomic()` 可以確保操作在一個事務內完成，任何一個操作失敗，整個事務會回滾。