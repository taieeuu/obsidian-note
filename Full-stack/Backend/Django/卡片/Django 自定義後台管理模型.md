---
Date: 2024-08-18
---
### 基本模型註冊
在應用的 `admin.py` 文件中，使用 `admin.site.register()` 方法來註冊模型：

```python
from django.contrib import admin
from .models import Book

admin.site.register(Book)
```

這樣，`Book` 模型就會出現在 Django Admin 的管理界面中，你可以對其數據進行基本的操作。
### 創建並註冊自定義管理模型類
為了自定義模型在 Django Admin 界面中的顯示方式和操作行為，你可以創建一個繼承自 `admin.ModelAdmin` 的自定義類，並將其註冊到 Django Admin。
#### 創建自定義管理模型類
在 `admin.py` 文件中，創建一個自定義的管理模型類，並覆蓋或擴展其中的屬性和方法來實現自定義行為。

```python
from django.contrib import admin
from .models import Book

class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'author', 'published_date', 'price')  # 在列表頁面顯示的字段
    search_fields = ('title', 'author__name')  # 添加搜索框，可以根據書名和作者名稱搜索
    list_filter = ('published_date', 'author')  # 添加篩選器，可以根據出版日期和作者篩選
    ordering = ('-published_date',)  # 默認按出版日期降序排列

admin.site.register(Book, BookAdmin)
```
#### 自定義列表顯示
`list_display` 用來控制模型在 Django Admin 列表頁面中顯示哪些字段。你可以指定多個字段，這些字段將以列的形式顯示在列表中。
#### 自定義搜索框
`search_fields` 屬性允許你在管理界面中添加一個搜索框，用來根據指定的字段進行搜索。你可以指定模型字段或者跨關聯模型的字段進行搜索（例如 `author__name`）。
#### 自定義篩選器
`list_filter` 屬性允許你在列表頁面的右側添加篩選選項，用來根據某些字段的值篩選數據。這在數據量較大時特別有用。
#### 自定義排序
`ordering` 屬性允許你指定默認的排序方式，例如按某個日期字段的降序排列。
### 高級自定義選項
除了基本的顯示和篩選，Django Admin 還提供了一些高級自定義選項，允許你更深入地控制管理界面的行為。
#### 自定義表單布局
通過 `fieldsets` 屬性，你可以自定義表單中字段的分組和顯示順序。

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

這樣，`title` 和 `author` 字段將顯示在一組，`published_date` 和 `price` 顯示在另一組，並且該組是可折疊的。
#### 自定義只讀字段
`readonly_fields` 屬性允許你設置在管理界面中顯示為只讀的字段。

```python
class BookAdmin(admin.ModelAdmin):
    readonly_fields = ('published_date',)
```

這樣，`published_date` 字段將顯示為只讀，無法編輯。
#### 自定義動作
Django Admin 允許你定義自定義的動作，這些動作可以批量應用於所選的模型記錄。例如，將選中的書籍標記為已發布：

```python
def mark_published(modeladmin, request, queryset):
    queryset.update(published_date=timezone.now())

class BookAdmin(admin.ModelAdmin):
    actions = [mark_published]
```
#### 自定義內聯（Inlines）
內聯顯示允許你在管理某個模型時，同時管理其關聯的子模型。這通常用於一對多或多對多的關係中。

```python
from .models import Chapter

class ChapterInline(admin.TabularInline):  # 也可以使用 StackedInline
    model = Chapter
    extra = 1

class BookAdmin(admin.ModelAdmin):
    inlines = [ChapterInline]
```

這樣，在管理書籍時，可以直接在同一個表單中管理其章節。
### 動態自定義（進階功能）
Django Admin 提供了一些方法，允許你根據當前用戶或對象狀態動態設置管理界面的行為。
#### 動態設置只讀字段
通過覆蓋 `get_readonly_fields` 方法，你可以動態設置哪些字段應該是只讀的。

```python
class BookAdmin(admin.ModelAdmin):
    def get_readonly_fields(self, request, obj=None):
        if not request.user.is_superuser:
            return ['price']
        return []
```

這樣，只有超級用戶可以編輯 `price` 字段。
#### 動態設置篩選器
你可以覆蓋 `get_list_filter` 方法來動態設置篩選器。

```python
class BookAdmin(admin.ModelAdmin):
    def get_list_filter(self, request):
        if request.user.is_superuser:
            return ['author', 'published_date', 'price']
        return ['author', 'published_date']
```

這樣，超級用戶會看到更多的篩選選項。

### 註冊裝飾器
除了常用的`admin.site.register()` 方法外，還可以通過使用裝飾器的方式連接模型和`ModelAdmin`類。

```python
from django.contrib import admin
from .models import Dep, Person

# ModelAdmin 模型，在此處註冊模型
@class.register(Dep, Person)
class PersonAdmin(admin.ModelAdmin):
	fields = ('name', 'dep)
```

通過 `@admin.register` 裝飾器註冊 `Dep` 模型和 `Person` 模型。