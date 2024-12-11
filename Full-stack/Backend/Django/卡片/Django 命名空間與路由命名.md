---
Date: 2024-08-18
---
命名空間允許你將 URL 名稱組織在一起，從而避免命名衝突。這對於大型應用程序或包含多個應用的項目特別有用。
### 應用命名空間
你可以在 `include()` 中指定一個命名空間來組織 URL。
#### 例子：使用命名空間
在主 `urls.py` 中：

```python
from django.urls import path, include

urlpatterns = [
    path('blog/', include(('blog.urls', 'blog'), namespace='blog')),
]
```

在 `blog` 應用的 `urls.py` 中，定義路由：

```python
from django.urls import path
from . import views

app_name = 'blog'  # 定義應用的命名空間 關鍵代碼

urlpatterns = [
    path('', views.index, name='index'),
    path('post/<int:id>/', views.post_detail, name='post_detail'),
]
```

然後在模板中，你可以這樣反向解析 URL：

```django html
<a href="{% url 'blog:index' %}">Blog Home</a>
```

