---
Date: 2024-08-17
---
Django 模型主要用來關聯資料庫，相當於一個 `ORM` 系統。，讓你可以使用 Python 程式碼來定義資料庫表格，並且以物件的形式操作資料庫中的記錄，而無需編寫 SQL 查詢。不過一些較複雜的操作，還是可以使用 SQL 語法來查詢。
### Django 模型的概念
##### 模型定義
- 每個 Django 模型通常對應資料庫中的一張表格，模型的每個屬性對應資料庫表中的一個欄位。
- 通過定義模型，你可以描述你的資料結構，並讓 Django 自動管理資料庫表的建立、修改和刪除。
##### 繼承自 models.Model
- 每個 Django 模型類別都繼承自 `django.db.models.Model`，這是 Django 提供的基類，包含了 ORM 的核心功能。
##### 欄位類型
- Django 提供了多種欄位類型，用來定義模型的屬性。例如，`CharField` 用來表示字串，`IntegerField` 用來表示整數，`DateTimeField` 用來表示日期時間等。
##### 元資料(Meta)
- Django 模型類別可以包含一個內部的 `Meta` 類別，用來定義模型的元資料，如排序規則、表格名稱等。
##### 查詢集(QuerySet)
- Django 提供了豐富的 API 來對模型進行查詢操作，返回的結果通常是 QuerySet 物件。QuerySet 是 Django 用來表示資料庫查詢結果的類別，可以對其進行篩選、排序等操作。
### 數據遷移
在項目中首次定義模型時，數據庫中尚未創建對應的數據表。此時，可以通過 Django 提供的操作指令，根據模型自動生成並創建數據表。
##### 生成遷移文件 
使用以下命令生成遷移文件，該文件包含了根據模型定義創建數據表的指令：

```shell
python manage.py makemigrations
```

執行成功後，Django 會在項目應用（app）的 `migrations` 資料夾中創建一個名為 `0001_initial.py` 的遷移文件。這個文件包含了根據 `models.py` 中定義的模型生成數據表的腳本代碼。
##### 應用遷移文件
使用以下命令應用剛剛生成的遷移文件，將數據表創建到數據庫中：

 ```sh
python manage.py migrate
```

該命令會執行 `0001_initial.py` 文件中的腳本代碼，根據模型定義在數據庫中創建相應的數據表。

而當開發者在項目中調整數據表結構時，Django 提供了一套完整的遷移系統來幫助管理這些變更。這些調整可能包括新增欄位、修改欄位類型、刪除欄位，甚至是重命名表格或欄位。Django 的遷移工具可以輕鬆地處理這些變更，確保數據庫結構和代碼中的模型定義始終保持一致。
##### 更新模型
當你在開發過程中需要修改模型，例如新增一個欄位或改變欄位的屬性時，首先需要在 `models.py` 中更新相應的模型定義。例如，為 `Book` 模型添加一個 `price` 欄位：

```python
class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    published_date = models.DateField()
    genre = models.CharField(max_length=100, default='Unknown')
    price = models.DecimalField(max_digits=6, decimal_places=2)  # 新增欄位
```
##### 生成新的遷移文件
更新模型後，需要生成對應的遷移文件，記錄這些變更。使用以下命令：

```sh
python manage.py makemigrations
```

此命令會在應用的 `migrations` 文件夾中生成一個新的遷移文件（如 `0002_add_price_to_book.py`），該文件包含了將 `price` 欄位添加到 `Book` 表的指令。
### 數據導入與導出
在 Django 中，如果數據表設有外鍵字段，導入數據時必須注意外鍵依賴關係。外鍵字段指向的是另一個數據表中的記錄，因此在導入數據時，必須先確保外鍵字段關聯的數據已經存在於數據庫中。否則，當 Django 嘗試導入具有外鍵關聯的數據時，會因為找不到相關的外鍵記錄而導致導入失敗。
##### 導入
```shell
python manage.py loaddata data.json
```
##### 導出
```shell
python manage.py dumpdata
```

一般情況下，數據導出和導入最好以整個項目或整個項目應用的數據為單位，因為數據表之間可能存在外鍵關聯，如果只導入某張表數據，就需要確認是否設有外鍵。