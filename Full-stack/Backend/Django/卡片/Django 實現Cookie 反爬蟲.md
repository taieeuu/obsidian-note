---
Date: 2024-08-25
---
服務器接收的 Http 請求信息包含有 `Cookie` 信息，`Cookie` 的作用是識別當前用戶的身分。

瀏覽器向服務器發送請求，服務器做出響應之後，二者便會斷開連接 (會話結束) ，下次用戶在來請求服務器時，服務器沒有辦法識別此用戶是誰。比如對於用戶登入功能，如果沒有 `Cookie` 機制的支持，那麼只能通過查詢數據庫實現，並且每次刷新頁面都要重新執行一次用戶登入操作才可以識別用戶，這會帶給伺服器巨大的負載壓力。

`Cookie` 從瀏覽器向服務器傳遞數據，讓服務器能夠識別當前用戶，而服務器對 `Cookie` 的識別機制是通過 `Session` 實現的，`Session` 儲存了當前用戶基本信息，如姓名、年齡等等。由於 `Cookie` 儲存在瀏覽器中，而且 `Cookie` 的數據事由服務器提供的，如果服務器將用戶信息直接保存在瀏覽器中，就很容易洩漏用戶信息，並且 `Cookie` 大小不能超過 `4KB` 不能支持中文。因此，需要一種機制在服務器的某個地方儲存用戶數據，這就是 `Session`。

總而言之，`Cookie` 和 `Session` 是為了解決 `Http` 協議無狀態的弊端外，為了讓瀏覽器和服務端建立長久聯繫的會話而出現。

`Cookie` 除了解決 `Http` 協議無狀態弊端之外，還可以實現反爬蟲機制。隨著大數據和人工智慧發展，爬蟲技術完善，網站為了維護自身數據安全性和負載能力，都會設置覽爬蟲機制。
### Cookie 實現反爬蟲
#### 設置 Cookie 的視圖
首先，你需要在 Django 的視圖中設置一個 Cookie，該 Cookie 可以用來標識合法的用戶請求。這通常在用戶訪問某些關鍵頁面時設置，例如登錄頁面、首頁等。

```python
from django.http import HttpResponse

def set_cookie_view(request):
    response = HttpResponse("Cookie has been set.")
    response.set_cookie('user_session', 'some_unique_value', max_age=3600)  # Cookie 有效期為 1 小時
    return response
```

在這個範例中，`set_cookie_view` 視圖將設置一個名為 `user_session` 的 Cookie，其值為 `some_unique_value`。這個值可以是隨機生成的、唯一的標識符，用於標識一個合法的訪問者。
#### 檢查 Cookie 的視圖
在需要保護的視圖中，你可以檢查請求是否攜帶了合法的 Cookie。如果 Cookie 不存在或者不正確，可以拒絕請求或重定向到其他頁面。

```python
from django.http import HttpResponse, HttpResponseForbidden

def protected_view(request):
    user_session = request.COOKIES.get('user_session')
    
    if user_session == 'some_unique_value':  # 比較 Cookie 的值
        return HttpResponse("Access granted.")
    else:
        return HttpResponseForbidden("Access denied. Possible bot detected.")
```

在這個範例中，`protected_view` 檢查 `user_session` Cookie 的值是否正確。如果 Cookie 存在且值正確，則允許訪問並返回 `Access granted.` 的響應；否則，返回 `403 Forbidden` 狀態碼並顯示 `Access denied. Possible bot detected.` 的信息。
### 請求頭實現反爬蟲
視圖的參數 `request` 是由類 `WSGIRequest` 根據用戶請求而生成的實例化對象。類 `WSGIRequest` 繼承並重寫 `HttpsRequest` 類，而 `HttpsRequest` 類設置了 `Meta` 屬性。因此，當用戶在瀏覽器中訪問 `Django` 時，通過 [[WSGI 協議]]時現了兩者之間的通信，瀏覽器的請求頭信息來自  `WSGIRequest` 的屬性`environ`，而屬性 `environ` 來自 `WSGIHandler`。

 WSGI 協議是描述服務器如何與瀏覽器通新的規範。Django 運行原理是在此規範上建立。所以我們知道 `Http` 請求頭信息來自屬性 `eviron`，請求頭信息是動態變化的，他可以自定義屬性，因此常被用於制定反爬蟲機制。

**注意** : 請求頭信息只能由瀏覽器進行設置，服務器只能讀取請求頭信息，通過請求頭實現反爬蟲機制一般需要借助前端實現。
#### 自定義頭部
請求中包含特定的自定義頭部信息，這種方法可以增加爬蟲模擬的難度。

**index/view.py**
```python
from django.http import JsonResponse
from django.shortcuts import render

def index(request):
    return render(request, 'index.html')
# API接口判斷請求頭
def getHeader(request):
    header = request.META.get('HTTP_SIGN', '')
    if header:
        value = {'header': header}
    else:
        value = {'header': 'null'}
    return JsonResponse(value)
```

**template/index.html**
```django html
<!DOCTYPE html>
<html>
{% load static %}
<script src="{% static 'jquery.js' %}"></script>
<script>
$(function(){
  $("#bt01").click(function(){
    var value = $.ajax({
        type: "GET",
        url:"/getHeader",
        dataType:'json',
        async: false,
        headers: {
            "sign":"123",
        }
        }).responseJSON;
        value = '<h2>'+value["header"]+'</h2>';
        $("#myDiv").html(value);
    });
});

</script>
<body>
<h3>Hello Headers</h3>
<div id="myDiv"><h2>AJAX獲取請求頭</h2></div>
<button id="bt01" type="button">改變内容</button>
</body>
</html>

```

- **`{% load static %}`**：這是一個 Django 模板標籤，用於載入靜態文件庫。這樣可以使用 `{% static %}` 標籤來引用靜態資源。
- **`<script src="{% static 'jquery.js' %}"></script>`** : 這行用於引入 jQuery 庫，通過 Django 的 `{% static %}` 標籤來指定靜態文件的路徑。這個靜態資源應該被放置在你的 Django 靜態文件目錄中。
- **`<script>`** : 這標籤內包含的是 JavaScript 程式碼。這段 JavaScript 使用了 jQuery 庫來處理網頁加載後的事件。
- **`$(function(){ ... });`** : 這是 jQuery 的文檔就緒函數，當整個網頁的 DOM 結構加載完成後，這段程式碼中的內容將會被執行。
- **`$("#bt01").click(function(){ ... });`** : 這段程式碼為 ID 為 `bt01` 的按鈕元素設置了一個點擊事件監聽器，當按鈕被點擊時，將會執行裡面的 AJAX 請求。
- **`var value = $.ajax({ ... }).responseJSON;`** : 這行程式碼發起了一個同步的 AJAX 請求（`async: false` 表示同步），請求的類型為 `GET`，目標 URL 是 `/getHeader`。請求的結果會以 JSON 格式返回，並被存儲在 `value` 變數中。
- **`headers: { "sign":"123", }`** : 這段程式碼在 AJAX 請求中添加了一個自定義的 HTTP 請求頭，名稱為 `sign`，值為 `123`。這種做法可以用來在後端驗證請求的合法性。
- **`value = '<h2>'+value["header"]+'</h2>';`** : 這行程式碼將從伺服器返回的 JSON 資料中的 `header` 字段提取出來，並將其包裹在 `<h2>` 標籤中，然後賦值給 `value` 變數。
- **`$("#myDiv").html(value);`** : 這行程式碼將 `value` 變數的內容插入到 ID 為 `myDiv` 的 `<div>` 元素中，替換原本的內容，從而動態地更新網頁的顯示內容。
#### 驗證 User-Agent
`User-Agent` 是一個用來標識發送請求的客戶端（如瀏覽器）的請求頭。許多爬蟲工具會使用預設的 `User-Agent`，這通常可以區分與普通瀏覽器的請求。

```python
from django.http import HttpResponseForbidden, HttpResponse

def check_user_agent(request):
    user_agent = request.META.get('HTTP_USER_AGENT', '')

    # 簡單檢查是否為常見瀏覽器的 User-Agent
    if 'Mozilla' in user_agent or 'Chrome' in user_agent or 'Safari' in user_agent:
        return HttpResponse("User-Agent is valid.")
    else:
        return HttpResponseForbidden("Access denied. Invalid User-Agent.")
```

在這個範例中，`check_user_agent` 檢查 `User-Agent` 是否包含常見瀏覽器的標識。如果不是，則返回 403 Forbidden 狀態碼，拒絕訪問。
#### 驗證 Accept-Encoding 和 Accept-Language
爬蟲通常不會像真實瀏覽器一樣細緻地設置 `Accept-Encoding` 和 `Accept-Language`。通過檢查這些頭部信息，可以進一步過濾掉非人類的請求。

```python
def check_accept_headers(request):
    accept_encoding = request.META.get('HTTP_ACCEPT_ENCODING', '')
    accept_language = request.META.get('HTTP_ACCEPT_LANGUAGE', '')

    # 檢查是否包含常見的編碼和語言設置
    if 'gzip' in accept_encoding and 'en-US' in accept_language:
        return HttpResponse("Accept headers are valid.")
    else:
        return HttpResponseForbidden("Access denied. Invalid Accept headers.")
```

在這個範例中，`check_accept_headers` 檢查請求是否包含常見的 `Accept-Encoding` 和 `Accept-Language` 設置。如果不匹配，則拒絕請求。
#### 結合多種頭部檢查
為了提高反爬蟲的有效性，建議將多種頭部檢查結合在一起。這樣可以增加爬蟲繞過保護措施的難度。

```python
def combined_check(request):
    user_agent = request.META.get('HTTP_USER_AGENT', '')
    referer = request.META.get('HTTP_REFERER', '')
    accept_encoding = request.META.get('HTTP_ACCEPT_ENCODING', '')
    accept_language = request.META.get('HTTP_ACCEPT_LANGUAGE', '')

    if ('Mozilla' in user_agent and 
        'example.com' in referer and 
        'gzip' in accept_encoding and 
        'en-US' in accept_language):
        return HttpResponse("All headers are valid.")
    else:
        return HttpResponseForbidden("Access denied. Invalid headers.")
```

這個視圖綜合了 `User-Agent`、`Referer`、`Accept-Encoding` 和 `Accept-Language` 的檢查。如果所有檢查都通過，則允許訪問，否則拒絕請求。
### 更高級的反爬蟲策略
除了簡單地檢查 Cookie，你還可以結合其他反爬蟲策略，增加爬蟲繞過的難度。
#### 設置和驗證隨機的動態 Cookie：
每次用戶訪問網站時，伺服器可以生成一個隨機的 Cookie，並要求後續請求攜帶正確的 Cookie 以通過驗證。

```python
import uuid
from django.http import HttpResponse

def set_dynamic_cookie(request):
    unique_value = str(uuid.uuid4())  # 生成唯一的 UUID
    response = HttpResponse("Dynamic cookie has been set.")
    response.set_cookie('user_session', unique_value, max_age=600)  # 有效期為10分鐘
    # 你可能還需要在伺服器端保存這個值以便後續檢查
    request.session['user_session'] = unique_value
    return response

def check_dynamic_cookie(request):
    user_session = request.COOKIES.get('user_session')
    session_user_session = request.session.get('user_session')
    
    if user_session == session_user_session:
        return HttpResponse("Dynamic cookie is valid.")
    else:
        return HttpResponseForbidden("Invalid cookie or session expired.")
```
#### IP 和 Cookie 結合：
你可以將 Cookie 和訪問者的 IP 地址結合起來進行檢查。這樣即使 Cookie 被爬蟲偽造，如果 IP 地址不一致，也可以拒絕訪問。

```python
def check_cookie_and_ip(request):
    user_session = request.COOKIES.get('user_session')
    session_user_session = request.session.get('user_session')
    user_ip = request.META.get('REMOTE_ADDR')
    session_ip = request.session.get('user_ip')
    
    if user_session == session_user_session and user_ip == session_ip:
        return HttpResponse("Cookie and IP are valid.")
    else:
        return HttpResponseForbidden("Invalid cookie or IP mismatch.")
```
### 實際應用考量
在實際應用中，單獨依賴 Cookie 並不能完全防止爬蟲。建議將 Cookie 驗證與其他反爬蟲技術（如速率限制、CAPTCHA、人機驗證等）結合使用，以達到更好的防護效果。