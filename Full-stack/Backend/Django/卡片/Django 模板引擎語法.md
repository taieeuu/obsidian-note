---
Date: 2024-08-17
---
Django 的模板引擎用於在 Django 項目中生成動態 HTML。模板引擎允許將 Python 的變量、條件邏輯和循環嵌入到 HTML 中，以便動態地生成頁面內容。

Django 模板語言主要分為 4 個部分，變量、標籤、過濾器和註解
### 變量
變量可以使用雙花括號 (`{{ }}`) 包圍來顯示。模板引擎會將變量替換為其對應的值。

```django html
<p>Hello, {{ user.username }}!</p>
```
### 標籤
標籤是一種執行邏輯或控制結構的語法。標籤使用 `{% %}` 包圍。注意 : 有些標籤需要同時使用開頭與結尾的標籤。

```django html
{% tag %} content {% endtag %}
```
##### 1. `{% if %}` and `{% if ... else %}
`{% if %}` 標籤用於執行條件語句，類似於 Python 的 `if` 語句。

```django html
{% if user.is_authenticated %}
    <p>Hello, {{ user.username }}!</p>
{% else %}
    <p>Please log in.</p>
{% endif %}
```
##### 2. `{% for %}`
`{% for %}` 標籤用於遍歷一個列表，並在每次迭代中執行某些操作。

```django html
<ul>
{% for item in items %}
    <li>{{ item.name }}</li>
{% endfor %}
</ul>
```
##### 3. `{% block %}`
`{% block %}` 標籤用於模板繼承中，用來定義可以在子模板中重寫的區塊。

```django html
{% block content %}
    <h1>Welcome to My Site</h1>
{% endblock %}
```
##### 4. `{% extends %}`
`{% extends %}` 標籤用來繼承另一個模板。通常用於建立基本模板，然後在具體頁面中擴展這些模板。

```django html
{% extends "base.html" %}
```
##### 5. `{% include %}`
`{% include %}` 標籤用來包含另一個模板的內容，類似於在多個頁面間重複使用的部分。

```django html
{% include "navbar.html" %}
```
##### 6. `{% csrf_token %}`
`{% csrf_token %}` 標籤用於在表單中生成 CSRF 保護的隱藏字段，以防止跨站請求偽造攻擊。

```django html
<form method="post">
    {% csrf_token %}
    <!-- form fields -->
</form>
```
##### 7. `{% url %}`
`{% url %}` 標籤用來反向解析 URL。這樣可以避免硬編碼 URL，並且在 URL 配置更改時仍能保持正常工作。

```django html
<a href="{% url 'app_name:view_name' %}">Link</a>
```
##### 8. `{% static %}`
`{% static %}` 標籤用於引用靜態文件（如 CSS、JavaScript 和圖像）。

```django html
<link rel="stylesheet" href="{% static 'css/styles.css' %}">
```
##### 9. `{% comment %}`
`{% comment %}` 標籤用來添加注釋，這些注釋不會被渲染到最終的 HTML 中。

```django html
{% comment %}
This is a comment that will not appear in the rendered HTML.
{% endcomment %}
```
##### 10. `{% with %}`
`{% with %}` 標籤用來將變量的值存儲在本地上下文中，可以減少重複的變量查詢。

```django html
{% with total=order.total price %}
    <p>Total: {{ total }}</p>
{% endwith %}
```
##### 11. `{% load %}`
`{% load %}` 標籤用於加載自定義模板標籤或過濾器庫。

```django html
{% load custom_tags %}
```
#### 自定義標籤
##### 創建一個 `templatetags` 目錄：
在你的應用中，創建一個名為 `templatetags` 的目錄，這個目錄應該包含一個 `__init__.py` 文件，以確保它被視為一個 Python 模組。

```sh
your_app/
    templatetags/
        __init__.py
        custom_tags.py
```
##### 編寫自定義標籤
在 `custom_tags.py` 中，編寫你的自定義標籤。首先導入 `template` 模組，然後使用裝飾器來定義標籤。

```python
from django import template

register = template.Library()

@register.simple_tag
def current_time(format_string):
    import datetime
    return datetime.datetime.now().strftime(format_string)
```

這裡的 `current_time` 是一個簡單標籤，它返回當前時間，並根據提供的格式字符串來格式化。
##### 在模板中加載並使用自定義標籤
在模板中，你需要使用 `{% load %}` 標籤來加載你剛才創建的自定義標籤。

```django html
{% load custom_tags %}

<p>The current time is: {% current_time "%Y-%m-%d %H:%M:%S" %}</p>
```

這會顯示當前時間，例如："The current time is: 2024-08-17 12:34:56"。
### 過濾器
過濾器用來修改變量的輸出。可以通過使用管道符號 (`|`) 來應用過濾器。
#### 格式
```django html
{{ name | filter }}
```

- name : 變量名稱
- filter : 過濾器
#### 範例
```django html
<p>Today is {{ date | date:"Y-m-d" }}</p>
```

這將會將 `date` 轉換為格式化的日期字符串。

過濾器也可以時線 **鏈結** 操作，通過串聯 `|` 管道來將一個過濾器輸出應用於下一個過濾器。 還有一些過濾器可以通過 `:` 來傳遞參數。