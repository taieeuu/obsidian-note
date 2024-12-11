---
Date: 2024-08-18
---
Django 允許你在 URL 中包含動態部分，這些動態部分可以作為參數傳遞給視圖。
#### 傳遞 URL 參數
你可以使用尖括號（`<>`）來定義動態 URL 部分，這些部分會被捕獲並作為參數傳遞給視圖。
##### 例子：捕獲動態參數
```python
from django.urls import path
from . import views

urlpatterns = [
    path('article/<int:id>/', views.article_detail, name='article_detail'),
]
```

在這個例子中，`<int:id>` 表示捕獲 URL 中的整數部分，並作為 `id` 參數傳遞給 `article_detail` 視圖。
##### 視圖中接收參數
```python
def article_detail(request, id):
    # 使用傳遞進來的 id 獲取對應的文章
    article = get_object_or_404(Article, pk=id)
    return render(request, 'article_detail.html', {'article': article})
```
#### 使用正則表達式
在 Django 2.0 之前，路由匹配主要依賴正則表達式。雖然現在 `path()` 更加直觀，但對於更複雜的路由模式，你仍然可以使用 `re_path()` 來使用正則表達式匹配 URL。
##### 例子：使用 `re_path()` 配置正則表達式路由
```python
from django.urls import re_path
from . import views

urlpatterns = [
    re_path(r'^article/(?P<slug>[-\w]+)/$', views.article_detail, name='article_detail'),
]
```

在這個例子中，`<slug>` 會匹配 URL 中的字母、數字、下劃線和連字符，並傳遞給視圖作為 `slug` 參數。