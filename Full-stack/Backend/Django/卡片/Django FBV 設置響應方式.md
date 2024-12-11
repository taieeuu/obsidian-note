---
Date: 2024-08-11
---
### 返回響應內容

| 響應類                                | 說明                     |
| ---------------------------------- | ---------------------- |
| HttpResponse('hello world')        | 狀態碼 200 ，請求已成功被伺服器接收   |
| HttpResponseRedirect('/')          | 狀態碼 302，重定向首頁地址        |
| HttpResponsePermanentRedirect('/') | 狀態碼 301，永久重定向首頁地址      |
| HttpResponseBadRequest('400')      | 狀態碼 400，訪問的頁面不存在或請求錯誤  |
| HttpResponseNotFound('404')        | 狀態碼 404，網頁不存在或網頁的URL失效 |
| HttpResponseForbidden('403')       | 狀態碼 403，沒有訪問權限         |
| HttpResponseNotAllowed('405')      | 狀態碼 405，不允許使用該請求方式     |
| HttpResponseServerError('500')     | 狀態碼 500，服務器內容錯誤        |
| JsonResponse({'foo':'bar'})        | 默認 200，響應內容為 json 數據   |
| StreamingHttpResponse()            | 默認 200，響應內容以流式輸出       |
上述的響應類主要來自模塊 `django.http` ，該模塊時響應功能的核心。

我們從源碼角度分析，打開響應類 `HttpResponse` 的源碼文件，可以發現響應類基本都是在 `HttpResponse` 基礎上實現的，只不過 `Http` 狀態碼有所不同而已。

從`HttpResponse`的使用過程可知，如果想要生成網頁內容，就需要將HTML語言已字符串形式表示，如果網頁內容過大，就會增加視圖的代碼量，同時也沒有體現模板的做用，所以 Django 在這基礎上進行封裝處理定義了函數 `render` 和 `redirect`。

```python
render (request, template_name, context=None, content_type=None, status=None, using=None)
```

* `request` : 瀏覽器向服務器發送請求對象，包含用戶信息、情球內容等。
* `template_name` : 模板文件名，用於生成網頁內容。
* `context` : 對模板上下文 ( 模板變量 ) 賦值，以字典格式表示。默認為空
* `content_type` : 響應內容的數據格式。一般情況默認值即可。
* `status` : http status code，默認200
* `using` : 設置模板引擎，用於解析模板文件，生成網頁內容。

為了說明，以下是一個簡單例子。

```python
# index的views.py
def index(request):
    value = {'title': 'Hello MyDjango'}
    return render(request, 'index.html', context=value)
    
# templates的index.html
<!DOCTYPE html>
<html>
	<body>
		<h3>{{ title }}</h3>
	</body>
</html>
```

視圖函數 index 定義的變量 value 作為 `render` 參數 `context` ，而模板 index.html 里通過使用模板上下文 ( 模板變量 ) `{{ title }}`來獲取變量 value 的數據，**上下文的命名必須與變量 value 的數據命名 ( 字典的key ) 相同，這樣才能將其匹配**，從而將參數 content 的數據轉換網頁內容。

但**如果視圖傳遞的變量過多，在設置 `context` 時就會顯得非常攏長，而不利於日後維護的更新。因此可以使用 python 內置語法 `locals()` 取代參數 `context`**，以下是從新編寫過後。

```python
# index的views.py
def index(request):
    title = {'key': 'Hello MyDjango'}
    content = {'key': 'This is MyDjango'}
    return render(request, 'index.html', locals())

# templates的index.html
<!DOCTYPE html>
<html>
	<body>
		<h3>{{ title.key }}</h3>
		<div>{{ content.key }}</div>
	</body>
</html>
```

`locals()` 會自動將變量 `title` 和 `content` 傳入模板文件，並由模板匹配上下文。因此所定義的變量名稱一定要相同。
### 設置重定向
重定向狀態碼為 301 和 302 ，前者屬於永久性跳轉的，後者是臨時跳轉的，兩者的區別在於搜索引擎的網頁抓取。
* 301 : 搜索引擎在抓取新內容的同時會將舊的網址替換為重定向之後的網址。
* 302 : 搜索引擎會抓取新內容而保留舊的網址。

重定向類 `HttpResponseRedirect` 和 `HttpResponsePermanentRedirect` 分別為 `http`狀態碼 302 和 301 ，而進入源碼查看也可以發現兩者都是繼承 `HttpResponseRedirectBase`類。

而文件裡還可以找到 `HttpResponseRedirectBase` 類的定義過程，發現該類繼承了響應類 `HttpResponse` ，並重寫了 `__init__` 和 `__repr__`。也就是說，Django的重定向是在響應類 `HttpResponse` 的基礎上進行功能重寫的，從而實現整個重定向過程。

`HttpResponseRedirect` 和 `HttpResponsePermanentRedirect` 的使用**只需傳入路由地址即可，兩者只支持路由地址而不支持路由命名的傳入**。為了進一步完善功能，Django 在此基礎上定義了重定向函數 `redirect` ，該函數支持路由地址或路由命名的傳入，並能通過函數參數來設置重定向的狀態碼。

```python
# index的urls.py
from django.urls import path
urlpatterns = [
    # 定義首页的路由
    path('', views.index, name='index'),
    # 定義商城的路由
    path('shop', views.shop, name='shop')
]
# index的views.py
from django.http import HttpResponseRedirect
from django.http import HttpResponsePermanentRedirect
from django.shortcuts import reverse
from django.shortcuts import render, redirect

def index(request):
    return redirect('index:shop' ,permanent=True)
    # 设置302的重定向
    # url = reverse('index:shop')
    # return HttpResponseRedirect(url)
    # 设置301的重定向
    # return HttpResponsePermanentRedirect(url)
def shop(request):
    return render(request, 'index.html')
```

視圖函數 index 的響應函數 `redirect` 將參數 `permanent` 設置為 True，並跳轉到路由命名為 shop 的網頁 ; 若調用 `HttpResponseRedirec` 或是 `HttpResponsePermanentRedirect`, 則需要跳用 `reverse` 函數將路由命名轉換成路由地址。從第三個方法來看，函數 `redirect` 更為便捷，也更符合 python 的設計思想。
### 異常響應
異常響應是指 `Http Status Code` 為 404 和 500 的響應狀態，他與正常響應過程一樣，指是狀態碼不一樣而已。因此使用 `render` 函數作為響應過程，並且設置參數`status` 404 和 500 ，即可實現異常響應。

同一個網站的每種異常響應所返回的頁面都是相同的，因此網站的異常響應必須適用於整個項目，也就是說要在Django配置全局的異常響應，必須在項目名的`urls.py`
配置。也因此需要在 templates 文件夾李新增`404.html` 和 `500.html`。

```python
# MyDjango的urls.py
from django.urls import path, include

urlpatterns = [
    # 指向index的路由文件urls.py
    path('', include(('index.urls', 'index'), namespace='index')),
]
# 全局404页面配置
handler404 = 'index.views.pag_not_found'
# 全局500页面配置
handler500 = 'index.views.page_error'

# index的views.py
from django.shortcuts import render
from django.http import Http404
def index(request):
    if request.GET.get('error', ''):
        raise Http404("page does not exist")
    else:
        return render(request, 'index.html')
def pag_not_found(request, exception):
    """全局404的配置函数 """
    return render(request, '404.html', status=404)
def page_error(request):
    """全局500的配置函数 """
    return render(request, '500.html', status=500)
```
### 文件下載功能
除了返回頁面信息，響應內容還可以實現文件下載功能。Django 提供三種方式來實現。分別為 `HttpResponse`、`SreamingHttpResponse` 和 `FileResponse`。
* `HttpResponse` 是所有響應過程的核心類，他的底層功能類是 `HttpResponseBase`。
* `StreamingHttpResponse` 是在 `HttpResponseBase` 的基礎上進行繼承與重寫的，他實現流是響應輸出 ( 使用 Python 的蝶帶器將數據進行分段處理並傳輸 )，是用於大規模數據輸出，指是用於文件傳輸響應。
* `FileResponse` 是在 `SreamingHttpResponse` 的基礎上進行繼承和重寫，他實現文件的流式響應輸出，指適用文件傳輸響應。

#### StreamingHttpResponse 
參數說明 : 
* `streaming_content` : 可設為迭代器對象或字節流，代表數據或文件內容
* `*args 和 **kwargs` : 用於設置 `HttpResponseBase` 的參數，即響應內容得數據格式 `content_type` 和響應狀態碼等等。

總得來說，`StreamingHttpResponse`實現文件下載功能，則文件式以字節的方式讀取，在`StreamingHttpResponse`實例化傳入文件的字節流。
#### FileResponse
該函數設置參數 `as_attachment` 和 `filename` 以及 `*args 和 **kwags`。參數說明如下。
* `as_attachment` : 若為 True，則不提供文件下載功能，文件將會在瀏覽器裡打開並讀取，若無法打開，則下載文件，反之。
* `filename` 設置下載文件的文件名，該參數與 `as_attachment` 相關，若 `as_attachment`為 False ，則 `filename` 不起任何作用，反之。
* `*args 和 **kwargs` : 用於設置 `HttpResponseBase` 的參數，即響應內容得數據格式 `content_type` 和響應狀態碼等等。
### 結論
`HttpResponse`、`SreamingHttpResponse` 和 `FileResponse`，都能實現下載功能，但三者之間存在一定的差異，說明如下。
* `HttpResponse` 實現文件下載存在很大弊端，其工作原理是將文件讀取並載入內存，然後將輸出到瀏覽器上實現下載功能。如果文件較大，該方法會占用很多內存。對於下載大文件，較推薦使用 `StreamingHttpResponse` 和 `FileResponse` 方法，這兩種方法將下載文件分批寫入服務器的硬盤，不是載入服務器內存。
* `StreamingHttpResponse` 和 `FileResponse` 實現原理相同。
* `StreamingHttpResponse` 的適用範圍更為廣泛，可支持大規模數據或文件輸出，而 `FileResponse` 指支持文件輸出。
* 從使用方式來看，`StreamingHttpResponse` 支持數據或文件輸出，因此在使用時需要設置響應輸出類型和方法，而 `FileResponse` 只需要設置3 個參數即可實現文件輸出。