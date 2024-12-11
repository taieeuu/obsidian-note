---
Date: 2024-08-17
---
在 Django 中，模型字段（Model Fields）是用來定義數據庫表中欄位的基本單位。每個模型字段對應於數據庫表中的一列，並且定義了數據的類型、驗證方式以及存儲格式。Django 提供了多種模型字段類型來適應不同的數據需求，這些字段類型可以幫助你輕鬆地構建和操作數據庫表。
### Django 模型字段類型
[官方模型字段類型文檔參考](https://docs.djangoproject.com/en/5.0/ref/models/fields/#field-types)

以下是一些常見的 Django 模型字段類型及其用途:
##### CharField
- 用於存儲字符數據（如短文本）。
- 需要指定 `max_length` 屬性來設置最大字符數。
- 示例 : `name = models.CharField(max_length=100)`
##### TextField
- 用於存儲長文本數據，沒有長度限制。
- 與 `CharField` 不同，`TextField` 適合用來存儲較長的文本，如文章內容。
- 示例：`description = models.TextField()`
##### IntegerField
- 用於存儲整數數據。
- Django 還提供了其他變體，如 `PositiveIntegerField`（只允許正數）和 `SmallIntegerField`（存儲範圍較小的整數）。
- 示例：`age = models.IntegerField()`
##### BooleanField
- 用於存儲布爾值（True/False）。
- 通常用於表示某種狀態或開關。
- 示例：`is_active = models.BooleanField(default=True)`
##### DateField and DateTimeField
- 用於存儲日期和時間數據。
- `DateField` 只存儲日期，`DateTimeField` 則存儲完整的日期和時間。
- 可以設置 `auto_now_add` 和 `auto_now` 屬性來自動設置字段值。
- 示例：`birthday = models.DateField()` and `created_at = models.DateTimeField(auto_now_add=True)`
##### FloatField and DecimalField
- 用於存儲浮點數和精確的小數。
- `DecimalField` 需要指定 `max_digits`（總位數）和 `decimal_places`（小數位數）屬性，適合存儲貨幣等精確數值。
- 示例 : `price = models.DecimalField(max_digits=10, decimal_places=2)` and `rating = models.FloatField()`
##### EmailField
- 用於存儲電子郵件地址，並自動執行基本的格式驗證。
- 示例：`email = models.EmailField()`
##### URLField
- 用於存儲 URL 並執行格式驗證。
- 示例：`website = models.URLField()`
##### FileField and ImageField
- 用於上傳文件和圖片。
- `ImageField` 是 `FileField` 的一種，並進行圖片格式驗證。
- 需要設置 `upload_to` 屬性來指定文件的存儲路徑。
- 示例：`file = models.FileField(upload_to='uploads/')` and 
`image = models.ImageField(upload_to='images/')`
##### ForeignKey、OneToOneField and ManyToManyField
- 用於建立模型之間的關聯關係。
- `ForeignKey` 用於建立多對一關係，`OneToOneField` 用於一對一關係，`ManyToManyField` 用於多對多關係。
- 示例：`author = models.ForeignKey(Author, on_delete=models.CASCADE)` and  `profile = models.OneToOneField(Profile, on_delete=models.CASCADE)` and `categories = models.ManyToManyField(Category)`
### 字段選項與屬性
[官方文檔字段選項參考](https://docs.djangoproject.com/en/5.0/ref/models/fields/#field-options)

每個模型字段類型都可以接受一組選項和屬性來控制其行為和特性，常見的有：

- **`null`**：設置為 `True` 時，允許字段在數據庫中存儲空值。
- **`blank`**：設置為 `True` 時，允許字段在表單中為空。
- **`default`**：設置字段的默認值。
- **`choices`**：設置字段的選擇範圍，通常用於有限的選項。
- **`unique`**：設置為 `True` 時，字段值在數據庫中必須唯一。