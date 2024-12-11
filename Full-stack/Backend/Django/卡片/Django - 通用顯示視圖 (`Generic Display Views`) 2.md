---
Date: 2024-08-17
---
Django 的 `Generic Display Views` 是一組通用視圖類別，主要用於顯示資料在網頁上，`Generic Display Views` 主要包含 `ListView` 和 `DetailView`，它們分別用於顯示對象列表和單個對象的詳細資訊。此外，還有一些特殊的視圖類別如 `TemplateView` 和 `RedirectView`，也屬於這一類別。
### ListView
`ListView` 是一個通用視圖類別，用於顯示某個模型的對象列表。它通常用於顯示資料庫中的多個對象，例如文章列表、用戶列表等。
#### 核心功能：
- 自動從資料庫中查詢所有對象，並將結果傳遞給模板進行渲染。
- 支援分頁功能，允許用戶在不同頁面間導航以查看更多結果。
#### 基本用法：
```python
from django.views.generic.list import ListView
from .models import Article

class ArticleListView(ListView):
    model = Article
    template_name = 'article_list.html'
    context_object_name = 'articles'
    paginate_by = 10
```
##### 說明：
- **`model = Article`**：指定視圖展示的模型是 `Article`。
- **`template_name = 'article_list.html'`**：指定使用的模板檔案。
- **`context_object_name = 'articles'`**：在模板中，使用 `'articles'` 作為對象列表的變數名稱。
- **`paginate_by = 10`**：啟用分頁功能，每頁顯示 10 條記錄。
#### 模板範例：
```django html
{% for article in articles %}
    <h2>{{ article.title }}</h2>
    <p>{{ article.content|truncatewords:30 }}</p>
    <a href="{{ article.get_absolute_url }}">閱讀更多</a>
{% endfor %}

{% if is_paginated %}
    <div class="pagination">
        <span class="step-links">
            {% if page_obj.has_previous %}
                <a href="?page=1">&laquo; 第一頁</a>
                <a href="?page={{ page_obj.previous_page_number }}">上一頁</a>
            {% endif %}
            
            <span class="current">
                第 {{ page_obj.number }} 頁，共 {{ page_obj.paginator.num_pages }} 頁。
            </span>
            
            {% if page_obj.has_next %}
                <a href="?page={{ page_obj.next_page_number }}">下一頁</a>
                <a href="?page={{ page_obj.paginator.num_pages }}">最後一頁 &raquo;</a>
            {% endif %}
        </span>
    </div>
{% endif %}
```
### DetailView
`DetailView` 是一個通用視圖類別，用於顯示單個對象的詳細資訊。它通常用於顯示資料庫中某個特定對象的詳細資料，例如文章詳情、用戶資訊等。
#### 核心功能：
- 自動從資料庫中獲取一個對象，並將其傳遞給模板進行渲染。
- 通常透過 URL 參數（如 `pk` 或 `slug`）來識別要顯示的對象。
#### 基本用法：
```python
from django.views.generic.detail import DetailView
from .models import Article

class ArticleDetailView(DetailView):
    model = Article
    template_name = 'article_detail.html'
    context_object_name = 'article'
```
##### 說明：
- **`model = Article`**：指定視圖展示的模型是 `Article`。
- **`template_name = 'article_detail.html'`**：指定使用的模板檔案。
- **`context_object_name = 'article'`**：在模板中，使用 `'article'` 作為對象的變數名稱。
#### 模板範例：
```django html
<h1>{{ article.title }}</h1>
<p>{{ article.content }}</p>
<p>發佈日期：{{ article.pub_date }}</p>
```
### TemplateView
`TemplateView` 是一個通用視圖類別，用於渲染一個靜態的模板頁面。它不依賴於資料庫模型，非常適合用於顯示靜態頁面，如“關於我們”頁面、幫助文檔頁面等。
#### 核心功能：
- 僅渲染模板，通常用於顯示靜態內容。
- 可以透過上下文變數傳遞額外的數據給模板。
#### 基本用法：
```python
from django.views.generic.base import TemplateView

class AboutView(TemplateView):
    template_name = 'about.html'
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['title'] = '關於我們'
        return context
```
##### 說明：
- **`template_name = 'about.html'`**：指定要渲染的模板檔案。
- **`get_context_data()`**：用於傳遞額外的上下文數據給模板。
#### 模板範例：
```django html
<h1>{{ title }}</h1>
<p>歡迎來到我們的網站！</p>
```
### RedirectView
`RedirectView` 是一個通用視圖類別，用於處理重定向。它通常用於在用戶訪問某個 URL 時，將他們重定向到另一個 URL。
#### 核心功能：
- 自動生成重定向響應，當用戶訪問時會被立即轉到指定的 URL。
- 可以基於 URL 參數生成動態重定向地址。
#### 基本用法：
```python
from django.views.generic.base import RedirectView

class GoToHomepage(RedirectView):
    url = '/'
```
##### 說明：
- **`url = '/'`**：指定用戶會被重定向到網站首頁。
