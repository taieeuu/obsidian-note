---
Date: 2024-08-12
tags:
  - Django
  - Python
---
數據顯示視圖是指將後台的數據展示在網頁上，數據主要來自模型。Django 提供給我們 4 個視圖類用於實現數據的展示，分別是 `RedirectView`、`TemplateView`、`ListView`、`DetailView`。

- `RedirectView` : 用於實現 `Http` 的重定向，默認情況下指定義 `Get` 請求方法。
- `TemplateView` : 視圖類的基礎視圖，可將數據傳遞給 HTML 模板，默認情況下指定義了 `Get` 請求方法。
- `ListView` : 在 `TemplateView` 的基礎上將數據以列表顯示，通常用於將某個數據表的數據以列表表示。
- `DetailView` : 在 `TemplateView` 的基礎上將數據嫌隙顯示，通常用於獲取數據表的單調數據。
### 重定向 RedirectView
視圖類 `RedirectView` 用於實現 `Http` 的重定向功能，即網頁轉跳。

視圖類 `RedirectView` 類繼承自父類 `View` ，其中父類 `View` 是指**所有視圖類的底層功能**。視圖類 `RedirectView` 定義 4 個屬性和 8 個類方法，

* `permanent` : 根據屬性值的真假來選擇重定向方式。True : 301、False : 302。
* `url` : 代表重定向路由地址。
* `pattern_name` : 代表重定向的路由命名，如以設置參數`url`，則無需設置參數。
* `query_string` : 是否將當前路由地址的請求參數傳遞到重定向的路由地址。
* `get_redirect_url()` : 根據屬性 `pattern_name` 所指向的路由命名來生成相應的路由地址。
* `get()` : 觸發 `Http` 的 Get 請求所執行的響應處理。
* 剩餘類方法 `head()`、`post()`、`options()`、`delete()`、`put()`、`patch()`，他們都是由 `Get()` 方法完成響應處理。

```python
from django.urls import path
from .views import *
urlpatterns = [
    # 定義路由
    path('', index, name='index'),
    path('turnTo', turnTo.as_view(), name='turnTo')
]

# index的views.py
from django.shortcuts import render
from django.views.generic.base import RedirectView

def index(request):
    return render(request, 'index.html')
    
class turnTo(RedirectView):
    permanent = False
    url = None
    pattern_name = 'index:index'
    query_string = True
    # 重寫 get_redirect_url
    def get_redirect_url(self, *args, **kwargs):
        print('This is get_redirect_url')
        return super().get_redirect_url(*args, **kwargs)
	# 重寫 get
    def get(self, request, *args, **kwargs):
        print(request.META.get('HTTP_USER_AGENT'))
        return super().get(request, *args, **kwargs)
# template的 index.html
<!DOCTYPE html>
<html>
<body>
<h3>Hello RedirectView</h3>
<a href="{% url 'index:turnTo' %}?k=1">ToTurn</a>
</body>
</html>
```

上述定義了視圖類 `turnTo` ，他繼承自父類 `RedirectView` ，對付類進行重寫，通過這種方式，可以對視圖類 `RedirectView` 進行擴展，且定義路由時，若使用視圖類處理 `Http` 請求，則**需要對視圖類調用 `as_view()` 方法，這是對視圖類進行實力化處理**。

**如果開發過程中需要對其他的 `Http` 請求進行處理，那麼指需要在視圖類 `turnto` 中重新定義相應的類方法即可。**
### 基礎視圖 TemplateView
視圖類是所有視圖類裡最基礎的視圖類，可直接調用。他繼承了多個父類 : `TemplateResponseMixin` 、`ContextMixin` 和 `View`。

從視圖類 `TemplateView` 源碼可以看到，他只定義類方法 `get()` ，他分別調用函數方法 `get_context_data()`、`render_to_response()`，從而完成 `Http` 請求的響應過程主要類方法 `get()` 所調用的函數主要來自父類，`TemplateResoponseMixin`、`ContextMixin`，如下圖。

視圖類 `TemplateView` 的 `get()`，所調用的函數，
- 視圖類 `ContextMixin` 的 `get_context_data()` 方法用於獲取模板上下文內容，模板上下文將視圖裡的數據傳遞到模板文件，再由模板引擎將數據轉換成 HTML 網頁數據。
- 視圖類 `TemplateResponseMixin` 的 `render_to_response()` 用於實現顯應處理，由顯應類 `TemplateResponse` 完成。

![[Django - 通用顯示視圖 (`Generic Display Views`) 1.jpg]]

視圖類 `TemplateView` 的源碼文件中，找到視圖類 `TemplateResponseMixin`的定義，該類設置 4 個屬性、2 個類，說明如下。
- `template_name` : 將模板文件 index.html 作為網頁文件。
- `template_engine` : 設置解析模板文件的模板引擎，默認`None`，即默認使用配置文件`settings.py` 的 TEMPLATES 所設置的解析模板引擎 `BACKEND`。
- `content_type` : 設置響應內容的數據格式，默認值為`None`，表示數據格視為 text/index
- `response_class` : 設置 `Http` 請求響應類，默認為響應類 `TemplateResponse`。
- `render_to_response()` : 實現響應處理，由響應類 `TemplateResponse` 完成。
- `get_template_names()` : 獲取屬性 `template_name` 的值。

```python
from django.urls import path
from .views import *
urlpatterns = [
    # 定義路由
    path('', index.as_view(), name='index'),
]
from django.views.generic.base import TemplateView

class index(TemplateView):
    template_name = 'index.html'
    template_engine = None
    content_type = None
    extra_context = {'title': 'This is GET'}
    # 重新定義模版上下文的獲取方式
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['value'] = 'I am MyDjango'
        return context
    # 定義HTTP的POST請求處理
    # 參數request代表HTTP請求信息
    # 若路由沒有變量，則可以參數kwargs裡獲得
    def post(self, request, *args, **kwargs):
        self.extra_context = {'title': 'This is POST'}
        context = self.get_context_data(**kwargs)
        return self.render_to_response(context)
```

將網站首頁的視圖函數改為視圖類，自訂義的視圖類 index 繼承視圖類 `TemplateView`，並重設 4 個屬性，重寫 2 個類。
- `template_name` : 將模板文件 index.html 作為網頁文件。
- `template_engine` : 設置解析模板文件的模板引擎，默認`None`，即默認使用配置文件`settings.py` 的 TEMPLATES 所設置的解析模板引擎 `BACKEND`。
- `content_type` : 設置響應內容的數據格式，默認值為`None`，表示數據格視為 text/index
- `extra_context` : 為模板文件的上下文 ( 模板變量 ) 設置變量值，可將數據轉換成網頁數據展示在瀏覽器上。
- `get_context_data()` : 繼承並重寫視圖類 `TemplateView` 的類方法，在變量 `contenxt` 裡新增數據 `value`。
- `post()` : 自定義 post 請求的處理方法，當觸發 Post 請求時，將會重設屬性 `extra_context` 的值，並調用 `get_context_data()` 將屬性 `extra_context` 重新寫入，從而實現動態改變模板上下文的數據內容。

在 `index.html` 中看到模板上下文 (變量) ，`{{ title }}` 和 `{{ value }}`，他們數據來源於 `get_context_data()`。
### 列表視圖 ListView
[官方文檔參考](https://docs.djangoproject.com/en/5.0/ref/class-based-views/generic-display/#listview)
用於顯示對象列表。`ListView` 提供了一個簡便的方式來從數據庫中檢索一個模型的所有對象，並將它們呈現在模板中，常用於查詢與展示。

![[Django - 通用顯示視圖 (`Generic Display Views`) 2.jpg]]

`ListView` 是由上述圖中這些底層類所組成的，在這些底層類的基礎上加入了模型的操作方法，從而得出是圖類 `ListView`。\

分析視圖類 `ListView` 的定義過程得知，`ListView` 具有視圖類 `TemplateView` 所有屬性和方法，因為兩者的底層類都是相同的。此外 `ListView` 還額外增加許多的屬性定義在 `MultipleObjectMixin` [參考文件](https://docs.djangoproject.com/en/5.0/ref/class-based-views/mixins-multiple-object/#multipleobjectmixin)。

### 詳細視圖 DetailView
