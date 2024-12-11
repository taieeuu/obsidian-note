---
Date: 2024-08-18
---
在 Django 中，每個模型（`Model`）類都可以包含一個名為 `Meta` 的內部類。這個 `Meta` 類用於定義模型的元數據，即一些不直接關聯模型字段的屬性。這些屬性可以用來改變模型的行為，如排序、數據庫表名的設置、唯一約束等。

[官方Meta類參考文件](https://docs.djangoproject.com/zh-hans/5.0/ref/models/options/#model-meta-options)
### Meta 類中的常用屬性
以下是 `Meta` 類中一些常見且重要的屬性，這些屬性允許你自定義 Django 模型的各種行為：
#### 1. db_table: 指定數據表的名稱
默認情況下，Django 使用模型名稱的複數形式作為數據表名。如果你想自定義數據表名稱，可以通過 `db_table` 屬性來實現。

```python
class MyModel(models.Model):
    name = models.CharField(max_length=100)

    class Meta:
        db_table = 'custom_table_name'
```

這樣，模型 `MyModel` 對應的數據表名將會是 `custom_table_name`。
#### 2. ordering : 指定默認排序
`ordering` 屬性用來設置查詢結果的默認排序方式。它是一個列表，列表中的字段將按照定義的順序進行排序。可以使用 `'-'` 符號指定降序排序。

```python
class MyModel(models.Model):
    name = models.CharField(max_length=100)
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        ordering = ['-created_at', 'name']
```

這樣，當你對 `MyModel` 進行查詢時，結果會首先按照 `created_at` 降序排序，如果時間相同，則按 `name` 升序排序。
#### 3. verbose_name 和 verbose_name_plural: 設置模型的易讀名稱
這兩個屬性用來定義模型在 Django 管理界面中顯示的名稱。

```python
class MyModel(models.Model):
    name = models.CharField(max_length=100)

    class Meta:
        verbose_name = 'My Custom Model'
        verbose_name_plural = 'My Custom Models'
```

- `verbose_name` 是模型的單數形式名稱。
- `verbose_name_plural` 是模型的複數形式名稱。
#### 4. unique_together : 定義複合唯一約束
`unique_together` 屬性用來設置複合唯一約束，即多個字段的組合必須是唯一的。

```python
class MyModel(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)

    class Meta:
        unique_together = ('first_name', 'last_name')
```

這意味著 `first_name` 和 `last_name` 的組合必須是唯一的，不能在表中出現兩條 `first_name` 和 `last_name` 都相同的記錄。
#### 5. index_together : 定義複合索引
`index_together` 屬性用來設置多個字段上的複合索引，這有助於提高查詢性能。

```python
class MyModel(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)

    class Meta:
        index_together = [('first_name', 'last_name')]
```

這將在 `first_name` 和 `last_name` 字段上創建一個複合索引。
#### 6. constraints : 定義數據庫約束
`constraints` 屬性允許你設置更加複雜的數據庫約束，例如 `CheckConstraint` 或 `UniqueConstraint`。

```python
from django.db.models import Q, CheckConstraint, UniqueConstraint

class MyModel(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()

    class Meta:
        constraints = [
            CheckConstraint(check=Q(age__gte=18), name='age_gte_18'),
            UniqueConstraint(fields=['name', 'age'], name='unique_name_age')
        ]
```

- `CheckConstraint` 用來檢查 `age` 字段的值是否大於或等於 18。
- `UniqueConstraint` 用來設置 `name` 和 `age` 的唯一約束。
#### 7. abstract : 定義抽象基類
如果將 `abstract` 設置為 `True`，那麼該模型就成為一個抽象基類，這意味著它不會對應到數據庫中的表，而是作為其他模型的基類來使用。

```python
class BaseModel(models.Model):
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True

class MyModel(BaseModel):
    name = models.CharField(max_length=100)
```

`BaseModel` 是一個抽象基類，`MyModel` 繼承了 `BaseModel` 的字段，但 `BaseModel` 不會創建對應的數據表。
#### 8. permissions : 自定義權限
`permissions` 屬性允許你定義自定義的權限，這些權限可以應用在 Django 管理界面或應用邏輯中。

```python
class MyModel(models.Model):
    name = models.CharField(max_length=100)

    class Meta:
        permissions = [
            ('can_publish', 'Can Publish Model'),
            ('can_archive', 'Can Archive Model'),
        ]
```

這將為 `MyModel` 添加兩個自定義權限 `can_publish` 和 `can_archive`，你可以在 Django 管理界面中分配這些權限。