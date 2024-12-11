---
Date: 2024-08-18
---
Django 路由系統（URL routing）負責將 URL 請求映射到相應的視圖（View）上。當用戶在瀏覽器中輸入 URL 或點擊鏈接時，Django 根據你在 `urls.py` 文件中定義的路由規則來決定應該調用哪個視圖來處理這個請求。
### 路由的作用
- **URL與視圖的對應**：Django 的路由系統將特定的 URL 對應到特定的視圖函數或類視圖，從而確保用戶請求能夠被正確處理。
- **動態參數的傳遞**：通過 URL 中的動態部分，你可以將請求中的參數傳遞給視圖，進行進一步的處理。
### 路由配置
Django 的路由規則通常定義在應用的 `urls.py` 文件中。你可以使用 `path()` 函數來定義路由規則。
#### 基本路由配置
`urls.py` 是每個 Django 應用的路由配置文件。你可以在其中定義 URL 模式與對應視圖之間的映射關係。
##### 例子：簡單的路由配置\
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home'),  # 根路徑對應到 home 視圖
    path('about/', views.about, name='about'),  # "about/" 對應到 about 視圖
]
```
#### 路由的名稱
每個路由都可以指定一個 `name`，這個名稱允許你在模板或代碼中使用反向解析來生成 URL，而不用硬編碼 URL 字符串。
##### 例子：使用命名路由
在 `urls.py` 中定義命名路由：

```python
urlpatterns = [
    path('about/', views.about, name='about'),
]
```

在模板中使用：

```django html
<a href="{% url 'about' %}">About Us</a>
```

這樣，當路由規則改變時，你只需要修改 `urls.py`，而不需要修改所有模板中的 URL。
### 正規表達式的路由定義
#### 使用 `re_path()` 定義路由
`re_path()` 函數允許你通過正規表達式來匹配 URL 路徑。這使得你能夠定義更加複雜的 URL 模式，並且可以根據需要捕獲 URL 中的變量部分。
##### 基本語法
```python
from django.urls import re_path
from . import views

urlpatterns = [
    re_path(r'^pattern$', views.view_function, name='route_name'),
]
```

- **`pattern`**: 使用正規表達式來定義 URL 匹配模式。
- **`view_function`**: 匹配成功後調用的視圖函數。
- **`name`**: 路由的名稱（可選），可以用於反向解析 URL。
### 正規表達式的基礎
在 `re_path()` 中使用的正規表達式允許你定義非常靈活的 URL 匹配邏輯。這裡列出一些常用的正規表達式符號及其含義：

- `^`：匹配字符串的開頭。
- `$`：匹配字符串的結尾。
- `[]`：匹配字符集中的任意一個字符。
- `(x|y)`：匹配 `x` 或 `y`。
- `(?P<name>pattern)`：命名捕獲組，匹配 `pattern` 並將結果命名為 `name`。
#### 範例與解釋
範例一：匹配年份的 URL

```python
from django.urls import re_path
from . import views

urlpatterns = [
    re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive, name='year_archive'),
]
```

這個例子中，`re_path()` 使用了正規表達式 `r'^articles/(?P<year>[0-9]{4})/$'` 來匹配類似 `articles/2024/` 的 URL。

- `^articles/`：URL 必須以 `articles/` 開頭。
- `(?P<year>[0-9]{4})`：捕獲一個四位數的年份，並將其命名為 `year`。
- `/$`：URL 必須以 `/` 結尾。