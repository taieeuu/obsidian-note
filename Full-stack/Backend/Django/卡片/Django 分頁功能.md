---
Date: 2024-08-21
---
在網頁上瀏覽數據時，通常會在數據列表的下方看到翻頁功能。Django 提供了一個強大且靈活的分頁功能，可以方便地將大量數據分成多個頁面展示。分頁功能在網站開發中非常常見，特別是在顯示查詢結果、文章列表、商品列表等場合。
### 使用 Django 的分頁器（Paginator）
Django 提供了 `Paginator` 類來幫助我們實現分頁。你可以將查詢集或列表數據傳遞給 `Paginator`，然後根據頁數生成對應的數據。
#### 基本使用
假設我們有一個包含多個文章的查詢集，我們希望將這些文章分成多個頁面，每頁顯示 10 篇文章：

```python
from django.core.paginator import Paginator
from django.shortcuts import render
from myapp.models import Article

def article_list(request):
    article_list = Article.objects.all()  # 查詢所有文章
    paginator = Paginator(article_list, 10)  # 每頁顯示 10 篇文章

    page_number = request.GET.get('page')  # 獲取當前頁數
    page_obj = paginator.get_page(page_number)  # 獲取當前頁面的文章

    return render(request, 'myapp/article_list.html', {'page_obj': page_obj})
```
#### 模板中的分頁顯示
在模板中，我們可以遍歷 `page_obj`，顯示當前頁面的文章，並使用 `paginator` 提供的方法來生成分頁控制器。

```django html
<!DOCTYPE html>
<html>
<head>
    <title>文章列表</title>
</head>
<body>
    <h1>文章列表</h1>
    <ul>
        {% for article in page_obj %}
            <li>{{ article.title }}</li>
        {% endfor %}
    </ul>

    <div class="pagination">
        <span class="step-links">
            {% if page_obj.has_previous %}
                <a href="?page=1">&laquo; 第一頁</a>
                <a href="?page={{ page_obj.previous_page_number }}">上一頁</a>
            {% endif %}

            <span class="current">
                第 {{ page_obj.number }} 頁，共 {{ page_obj.paginator.num_pages }} 頁
            </span>

            {% if page_obj.has_next %}
                <a href="?page={{ page_obj.next_page_number }}">下一頁</a>
                <a href="?page={{ page_obj.paginator.num_pages }}">最後一頁 &raquo;</a>
            {% endif %}
        </span>
    </div>
</body>
</html>
```
### Paginator 類和 Page 類
- **Paginator**：負責將數據集分成多個頁面。主要屬性和方法包括：
    - `count`：返回總項目數。
    - `num_pages`：返回總頁數。
    - `page_range`：返回頁碼範圍的可迭代對象。
    - `get_page(number)`：返回指定頁碼的 `Page` 對象，如果頁碼無效，則返回空頁。
- **Page**：代表一個具體的頁面，主要屬性和方法包括：
    - `object_list`：當前頁面的數據集。
    - `number`：當前頁碼。
    - `paginator`：指向對應的 `Paginator` 對象。
    - `has_next()`：是否有下一頁。
    - `has_previous()`：是否有上一頁。
    - `next_page_number()`：下一頁的頁碼。
    - `previous_page_number()`：上一頁的頁碼。
### 處理無效的頁碼
在使用 `Paginator` 的時候，我們應該處理用戶輸入無效頁碼的情況。例如，頁碼超過了有效範圍，或者頁碼不是有效的數字。這可以通過捕獲 `Paginator` 引發的異常來實現：

```python
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger

def article_list(request):
    article_list = Article.objects.all()
    paginator = Paginator(article_list, 10)

    page_number = request.GET.get('page')

    try:
        page_obj = paginator.page(page_number)
    except PageNotAnInteger:
        # 如果頁碼不是一個整數，則顯示第一頁
        page_obj = paginator.page(1)
    except EmptyPage:
        # 如果頁碼超出了範圍，顯示最後一頁
        page_obj = paginator.page(paginator.num_pages)

    return render(request, 'myapp/article_list.html', {'page_obj': page_obj})
```
### 高級分頁功能
- **自定義分頁大小**：允許用戶選擇每頁顯示的數量，可以通過 GET 參數來控制。

```python
def article_list(request):
    article_list = Article.objects.all()
    page_size = request.GET.get('page_size', 10)  # 默認每頁 10 條
    paginator = Paginator(article_list, page_size)

    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)

    return render(request, 'myapp/article_list.html', {'page_obj': page_obj})
]
```

- **分頁器顯示範圍**：如果頁碼很多，可能希望只顯示相鄰的幾個頁碼，而不是所有頁碼，可以自定義頁碼範圍。