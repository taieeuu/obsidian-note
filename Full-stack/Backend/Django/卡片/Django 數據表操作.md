---
Date: 2024-08-18
---
Django 框架提供一個 ORM 框架實現數據新增、修改、刪除、查詢、執行SQL語句何時線數據庫業務等操作。
### 數據新增
Django ORM 通過模型類的實例來插入數據。可以使用 `save()` 方法保存新的數據記錄到數據庫，也可以使用 `Model.objects.create()` 直接創建並保存。

```python
# 創建實例並保存
instance = MyModel(name="John Doe", age=30, email="john@example.com")
instance.save()

# 使用 create 方法直接插入數據
MyModel.objects.create(name="Jane Doe", age=25, email="jane@example.com")

# 同樣調用create方法，但數據以字典格是表示
d = dict(name="Jane Doe", age=25, email="jane@example.com")
v = MyModel.objects.create(**d)
```

當然也可以直接在 `python manage.py shell` 中進行創建。

為了保證數據的有效性，避免重複插入數據，通常需要在插入數據之前進行去重判斷。這可以通過在插入數據前先進行查詢操作來實現。以下是一些常見的去重判斷方法：
##### 1. 使用 `get_or_create` 方法
Django 的 `get_or_create` 方法可以用來確保數據不會重複插入。它會先嘗試在數據庫中查找匹配的記錄，如果不存在則創建新的記錄。

```python
instance, created = MyModel.objects.get_or_create(
    name="John Doe",
    defaults={'age': 30, 'email': 'john@example.com'}
)
```
##### 2. 手動去重判斷
可以在插入數據之前進行手動查詢，來判斷是否已存在重複的記錄。如果記錄不存在，則插入新數據。

```python
if not MyModel.objects.filter(name="John Doe").exists():
    MyModel.objects.create(name="John Doe", age=30, email="john@example.com")
else:
    print("該數據已存在，未進行插入")
```

- `filter()` 方法根據條件查詢數據庫。
- `exists()` 方法判斷是否有匹配的記錄，返回 `True` 或 `False`。
##### 3. 使用唯一約束
在模型字段上設置 `unique=True`，確保在數據庫層面上自動進行去重判斷。這種方式不僅能避免重複插入，也能提高數據的完整性。

```python
class MyModel(models.Model):
    name = models.CharField(max_length=100, unique=True)
    age = models.IntegerField()
    email = models.EmailField()
```

這樣，當你嘗試插入重複的 `name` 時，Django 會拋出 `IntegrityError` 異常，提示違反唯一約束。
##### 4. 捕獲異常處理
如果使用唯一約束來避免重複插入，也可以捕獲 `IntegrityError` 異常來進行特定處理。

```python
from django.db import IntegrityError

try:
    MyModel.objects.create(name="John Doe", age=30, email="john@example.com")
except IntegrityError:
    print("該數據已存在，未進行插入")
```
### 數據的修改
修改數據通常是通過獲取數據庫中的記錄，然後更改屬性值，最後調用 `save()` 方法來保存更新。此外，還可以使用 `update()` 方法批量更新多條記錄。

```python
# 獲取實例並更新
instance = MyModel.objects.get(id=1)
instance.name = "New Name"
instance.save()

# 批量更新
MyModel.objects.filter(age=30).update(age=31)

# 更新數據以字典格式表示
d = dict(age=30)
Model.objects.filter(age=10).update(**d)

# 不使用查詢方法，默認對全表進行數據進行更新
Model.objects.update(age=40)

# 使用內置 F 方法實現數據的自增或自減
from django.db.models.import F
v = Models.objects.filter(age=10)

# 將payment字段原有數據自增加1
v.update(payment=F('payment')+1)
```

`F` 對象位於 `django.db.models` 模塊內，用於在查詢中引用數據庫中的字段值。它常用於進行字段間的比較、增量更新等操作。

```python
from django.db.models import F

# 假設有一個模型 Book，有一個字段名為 'title'
# 我們要將所有書名為 "Old Title" 的書籍，更新為 "New Title"
Book.objects.filter(title="Old Title").update(title=F('new_title_field'))
```

Django 2.2或以上版本，新增了數據批量更新方法 `bulk_update`，用來對多個模型實例進行批量更新。這一方法極大地提高了對多條記錄同時更新的效率，特別是在需要更新大量記錄的情況下。
#### `bulk_update` 方法的使用
`bulk_update` 方法允許你一次性更新多個實例的指定字段，無需分別調用每個實例的 `save()` 方法。這樣可以大幅減少與數據庫的交互次數，從而提高性能。
##### 使用示例
假設我們有一個模型 `Book`，它有兩個字段：`title` 和 `author`，並且我們想要批量更新多個 `Book` 實例的 `title` 字段。

```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=100)
```

假設我們已經有一些 `Book` 實例，我們現在需要批量更新它們的 `title`。

```python
# 假設我們有一些書籍需要更新
books = Book.objects.filter(author='J.K. Rowling')

# 修改這些書籍的標題
for book in books:
    book.title = "New Title"

# 使用 bulk_update 方法批量更新
Book.objects.bulk_update(books, ['title'])
```
##### `bulk_update` 方法參數
- **`objects`**: 一個模型實例的列表，這些實例是需要更新的對象。
- **`fields`**: 一個字段名稱的列表，指定了需要更新的字段。
#### 注意事項
1. **主鍵必須存在**：`bulk_update` 方法要求所有需要更新的實例必須有一個有效的主鍵（即數據庫中的 ID），因此它不能用於創建新記錄。
2. **僅適用於特定字段**：`bulk_update` 只會更新你指定的字段，其他字段不會受到影響。
3. **無信號支持**：使用 `bulk_update` 時，Django 不會觸發 `pre_save` 和 `post_save` 信號。如果你的應用依賴這些信號來執行其他邏輯，這點需要特別注意。
### 數據刪除
數據刪除有三種方式，刪除數據表的全部數據、刪除單一數據和刪除多條數據。
#### 刪除數據表的全部數據
刪除數據表中的所有數據可以使用 Django 的 `QuerySet.delete()` 方法。這個方法會刪除該模型表中的所有記錄，但保留表的結構。

```python
# 刪除 Book 表中的所有數據
Book.objects.all().delete()
```

- 這個操作相當於執行 `DELETE FROM book;` 的 SQL 語句。
- 注意，這將刪除表中所有的記錄，因此需要謹慎操作。
#### 刪除單一數據
刪除單一數據是指根據主鍵（或其他唯一標識符）找到特定的記錄，然後將其刪除。可以先查詢出這條記錄，然後調用實例的 `delete()` 方法。

```python
# 查找並刪除 id 為 1 的書籍
book = Book.objects.get(id=1)
book.delete()
```

- 這個操作會執行針對該記錄的刪除操作。
- 如果查找的記錄不存在，`get()` 方法會拋出 `DoesNotExist` 異常，因此可以考慮使用 `try-except` 來處理這種情況。
#### 刪除多條數據\
刪除多條數據通常涉及到根據某些條件篩選出多條記錄，然後批量刪除。這可以使用 `QuerySet.delete()` 方法來實現。

```python
# 刪除所有作者為 "J.K. Rowling" 的書籍
Book.objects.filter(author="J.K. Rowling").delete()
```

- 這個操作會刪除所有符合條件的記錄。
- 與刪除全部數據類似，這也是一個批量操作，但僅針對符合篩選條件的記錄。
#### 刪除操作的注意事項
1. **信號支持**：在刪除操作中，Django 會觸發 `pre_delete` 和 `post_delete` 信號。如果你有自定義的邏輯與這些信號相關，這點非常重要。
2. **外鍵約束**：在有外鍵關聯的情況下，刪除操作可能會連帶刪除相關的記錄，具體取決於模型中 `on_delete` 的設置（如 `CASCADE`、`SET_NULL` 等）。
3. **性能考慮**：批量刪除操作比逐條刪除效率更高，尤其是在需要刪除大量數據時。
