---
Date: 2024-08-18
---
Django Admin 二次開發是指在 Django Admin 後台管理系統的基礎上進行進一步的自定義和擴展，以滿足特定的業務需求。這些自定義通常包括修改界面顯示、添加自定義功能、調整操作流程，甚至是集成外部工具和服務。
### Admin的二次開發
#### get_readonly_fields
假設有一個模型 `Book`，你希望在管理界面中根據當前用戶的權限來動態地設置某些字段為只讀。

```python
from django.contrib import admin
from .models import Book

class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'author', 'published_date', 'price')

    def get_readonly_fields(self, request, obj=None):
        # 如果用戶不是超級用戶，將 'price' 字段設為只讀
        if not request.user.is_superuser:
            return ['price']
        return []

admin.site.register(Book, BookAdmin)
```

- **`get_readonly_fields` 方法**：這個方法接收兩個參數 `request` 和 `obj`。
    - `request` 是當前的 HTTP 請求對象，包含了當前用戶的信息。
    - `obj` 是當前正在編輯的模型實例，可能是 `None`（如果是新建對象）。
- **動態設置只讀字段**：
    - 在這個例子中，我們檢查當前用戶是否是超級用戶（`is_superuser`）。如果不是超級用戶，那麼我們將 `price` 字段設置為只讀。
    - 如果是超級用戶，則返回一個空列表，意味著沒有字段是只讀的。
- **`readonly_fields` 屬性**：
    - `readonly_fields` 屬性本身是一個靜態的屬性，用於定義哪些字段應該在管理界面中是只讀的。如果你需要動態設置，只讀字段應該使用 `get_readonly_fields` 方法。
#### get_list_display
`get_list_display` 方法允許你動態設置在 Django Admin 列表頁面中顯示的字段。這與靜態的 `list_display` 屬性類似，但 `get_list_display` 可以根據請求或對象狀態來調整顯示的字段。

```python
class BookAdmin(admin.ModelAdmin):
    def get_list_display(self, request):
        if request.user.is_superuser:
            return ('title', 'author', 'published_date', 'price')
        return ('title', 'author', 'published_date')

admin.site.register(Book, BookAdmin)
```

在這個例子中，超級用戶可以看到 `price` 字段，而其他用戶只能看到 `title`、`author` 和 `published_date`。
#### get_search_fields
`get_search_fields` 方法允許你根據請求動態設置可搜索的字段。這與 `search_fields` 屬性類似，但可以根據特定條件進行動態設置。

```python
class BookAdmin(admin.ModelAdmin):
    def get_search_fields(self, request):
        if request.user.is_superuser:
            return ('title', 'author__name', 'isbn')
        return ('title', 'author__name')

admin.site.register(Book, BookAdmin)
```

這樣，超級用戶可以額外按 ISBN 搜索，而普通用戶只能按書名和作者搜索。
#### get_list_filter
`get_list_filter` 方法允許你動態設置在 Django Admin 列表頁面右側顯示的篩選器。

```python
class BookAdmin(admin.ModelAdmin):
    def get_list_filter(self, request):
        if request.user.is_superuser:
            return ('author', 'published_date', 'price')
        return ('author', 'published_date')

admin.site.register(Book, BookAdmin)
```

這樣，超級用戶會看到價格篩選器，而普通用戶則不會。
#### get_ordering
`get_ordering` 方法允許你動態設置默認的排序方式。這與靜態的 `ordering` 屬性類似，但 `get_ordering` 可以根據請求或對象狀態進行調整。

```python
class BookAdmin(admin.ModelAdmin):
    def get_ordering(self, request):
        if request.user.is_superuser:
            return ['-published_date', 'title']
        return ['title']

admin.site.register(Book, BookAdmin)
```

這樣，超級用戶會按 `published_date` 降序排列，而普通用戶按 `title` 排序。
#### get_fieldsets
`get_fieldsets` 方法允許你動態設置模型表單的字段分組。這與 `fieldsets` 屬性類似，但 `get_fieldsets` 可以根據不同情況進行調整。

```python
class BookAdmin(admin.ModelAdmin):
    def get_fieldsets(self, request, obj=None):
        if obj and obj.published_date:
            return (
                (None, {'fields': ('title', 'author')}),
                ('Advanced options', {'fields': ('published_date', 'price')}),
            )
        return (
            (None, {'fields': ('title', 'author', 'published_date', 'price')}),
        )

admin.site.register(Book, BookAdmin)
```

在這個例子中，當書籍已經發布時，`published_date` 和 `price` 會被放到一個單獨的字段組中。
#### get_exclude
`get_exclude` 方法允許你動態設置在表單中需要排除的字段。這與 `exclude` 屬性類似，但可以根據請求或對象狀態動態排除字段。

```python
class BookAdmin(admin.ModelAdmin):
    def get_exclude(self, request, obj=None):
        if not request.user.is_superuser:
            return ['isbn']
        return []

admin.site.register(Book, BookAdmin)
```

在這個例子中，普通用戶無法在表單中看到或編輯 `isbn` 字段。
#### get_prepopulated_fields
`get_prepopulated_fields` 方法允許你動態設置哪些字段應該被預填充。這通常用於生成 `slug` 字段。

```python
class BookAdmin(admin.ModelAdmin):
    def get_prepopulated_fields(self, request, obj=None):
        if obj:
            return {'slug': ('title',)}
        return {}

admin.site.register(Book, BookAdmin)
```

這樣，當編輯現有書籍時，`slug` 字段會根據 `title` 自動生成。
#### get_inline_instances
`get_inline_instances` 方法允許你動態設置在管理界面中顯示的內聯表單（inlines）。這與靜態的 `inlines` 屬性類似，但可以根據請求或對象狀態進行調整。

```python
class BookAdmin(admin.ModelAdmin):
    def get_inline_instances(self, request, obj=None):
        inlines = []
        if request.user.is_superuser:
            inlines.append(SpecialInline)
        if obj and obj.published_date:
            inlines.append(ReviewInline)
        return inlines

admin.site.register(Book, BookAdmin)
```

這樣，只有超級用戶和已發布的書籍才會顯示相應的內聯表單。

#### 結論
Django Admin 提供了大量的鉤子方法，允許你根據請求、用戶權限、對象狀態等因素動態定制管理界面中的各種行為。這些方法包括但不限於 `get_readonly_fields`、`get_list_display`、`get_search_fields`、`get_list_filter` 等，它們可以幫助你打造更加靈活、智能的 Django Admin 界面，滿足不同的業務需求。