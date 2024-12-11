---
Date: 2024-08-18
---
Django 內置了一個功能強大的 Admin 後台管理系統，當你創建 Django 項目時，這個系統會默認啟用。Django Admin 提供了一個靈活的界面來管理你的模型數據，並且幾乎不需要額外的代碼即可使用。
### 1. 啟用 Django Admin
當你創建一個新的 Django 項目時，Admin 系統通常是自動啟用的。你可以在 `INSTALLED_APPS` 設置中看到它：

```python
INSTALLED_APPS = [
    ...
    # 在這裡
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    ...
]
```
#### 2. 創建超級用戶
在使用 Django Admin 界面之前，你需要創建一個超級用戶（superuser），它具有訪問後台管理界面和管理所有數據的權限。

```python
python manage.py createsuperuser
```

你會被要求提供用戶名、電子郵件地址和密碼。創建完成後，超級用戶將能夠登錄 Django Admin 界面。
#### 3. 運行開發服務器並訪問 Admin 界面
啟動 Django 開發服務器：

```python
python manage.py runserver
```

然後在瀏覽器中訪問 `http://127.0.0.1:8000/admin/`，輸入剛剛創建的超級用戶憑據，即可進入 Django Admin 後台管理界面。
#### 5. 自定義 Admin 界面
Django Admin 提供了大量的自定義選項，讓你可以更好地控制模型在管理界面的顯示和行為。
##### ModelAdmin 類
你可以通過繼承 `admin.ModelAdmin` 並覆蓋其屬性來自定義模型的管理界面。

```python
from django.contrib import admin
from .models import Book

class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'author', 'published_date', 'price')
    search_fields = ('title', 'author__name')
    list_filter = ('published_date', 'author')
    ordering = ('-published_date',)

admin.site.register(Book, BookAdmin)
```

- **`list_display`**：指定在列表頁面顯示哪些字段。
- **`search_fields`**：在頁面上添加搜索框，根據指定字段進行搜索。
- **`list_filter`**：在頁面右側添加篩選器，根據某些字段進行篩選。
- **`ordering`**：指定默認的排序方式。
#### 6. 自定義模型表單
你可以使用 `fieldsets` 和 `fields` 來自定義模型表單的布局，將字段分組或者重新排序。
##### fieldsets：
用來分組顯示字段，可以指定每個組的標題和顯示樣式。

```python
class BookAdmin(admin.ModelAdmin):
    fieldsets = (
        (None, {
            'fields': ('title', 'author')
        }),
        ('Advanced options', {
            'classes': ('collapse',),
            'fields': ('published_date', 'price'),
        }),
    )
```
##### fields：
簡單地指定表單中字段的顯示順序。

```python
class BookAdmin(admin.ModelAdmin):
    fields = ['title', 'author', 'published_date', 'price']
```

#### 7. 自定義動作
Django Admin 允許你定義自定義的管理動作，這些動作可以批量應用於所選的模型記錄。

```python
from django.contrib import admin
from .models import Book

def mark_published(modeladmin, request, queryset):
    queryset.update(published_date=timezone.now())

class BookAdmin(admin.ModelAdmin):
    actions = [mark_published]

admin.site.register(Book, BookAdmin)
```

在這個示例中，我們定義了一個 `mark_published` 動作，它將所選的書籍標記為已發布。
#### 8. 管理界面國際化
Django Admin 支持多語言和國際化，你可以通過設置 `LANGUAGE_CODE` 和 `LOCALE_PATHS` 來實現管理界面的本地化。

```python
LANGUAGE_CODE = 'zh-hans'

LOCALE_PATHS = [
    os.path.join(BASE_DIR, 'locale'),
]
```

這樣，Django Admin 界面就會根據設置顯示相應語言。