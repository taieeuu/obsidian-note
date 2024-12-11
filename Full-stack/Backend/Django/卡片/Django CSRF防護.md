---
Date: 2024-08-21
---
跨站請求偽造（Cross-Site Request Forgery, CSRF）是一種常見的網絡攻擊方式，攻擊者通過欺騙用戶在不知情的情況下執行未授權的操作來達到攻擊目的。為了防止這類攻擊，Django 提供了內建的 CSRF 防護機制。

可參考[[CSRF (跨站請求偽造)]]
### Django 的 CSRF 防護機制
Django 使用 CSRF token 來防止這類攻擊。每當用戶發送需要修改伺服器狀態的請求（例如 POST、PUT、DELETE），Django 要求請求中包含一個有效的 CSRF token。**這個 token 只有在 Django 生成並通過 HTML 表單或 AJAX 請求中的 JavaScript 發送回伺服器時才是有效的。**

原理如下: 
- 在用戶訪問網站時，Django 在網頁表單中生成一個隱藏控鍵 `csrfmiddlewaretoken` 控鍵屬性 value 的值是由 Django 隨機生成。
- 當用戶提交表單時，Django 校驗表單的 `csrfmiddlewaretoken` 是否與自己保存的 `csrfmiddlewaretoken` 一致，以此來判斷當前請求是否合法。
- 如果用戶被 CSRF 攻擊並從其他地方發送攻擊請求，但由於其他地方不可能知道隱藏控鍵 `csrfmiddlewaretoken` 的值，因此導致網站後台校驗 `csrfmiddlewaretoken` 失敗，使得攻擊被成功防禦。
### 啟用 CSRF 防護
Django 默認情況下啟用了 CSRF 防護。你可以在中介軟體（middleware）中看到如下配置：

```python
# myproject/settings.py

MIDDLEWARE = [
	# `CsrfViewMiddleware` 負責檢查請求是否包含有效的 CSRF token。
    'django.middleware.csrf.CsrfViewMiddleware',
    # 其他中介軟體
]
```

CSRF防禦只適用於 `POST` 請求，並不防禦 `GET` ，因為 `GET` 請求是以只獨行是訪問網站資源，一般情況下並不破壞和竄改網站數據。
### 在模板中使用 CSRF token
在使用 Django 模板引擎生成 HTML 表單時，你需要在表單中添加 `{% csrf_token %}` 標籤來插入 CSRF token。這樣當用戶提交表單時，CSRF token 會隨著請求一起發送到伺服器。

```django html
<form method="post">
    {% csrf_token %}
    <!-- 其他表單字段 -->
    <input type="submit" value="Submit">
</form>
```

我們可以使用 `F12` 查看，用戶每次提交表單或刷新網頁，隱藏控鍵 `csrfmiddlewaretoken` 的屬性 `value` 都會隨之變化。
### @csrf_exempt
如果想要取消表單的 `CSRF` 防護，那麼可以在模板文件上刪除 `{% csrf_token %}`，並在對應的視圖函數中添加裝飾器 `@csrf_exempt`。

```python
from django.shortcuts import render
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def index(request):
	return render(request, 'index.html')
```

如果只在文件上刪除  `{% csrf_token %}`，並沒有在對應的視圖函數中設置過濾器 `@csrf_exempt`，那麼用戶提交表單時，程序會因 `CSRF` 驗證失敗而拋出 `403`。

如果整個網站無須用到 `CSRF` ，可在 `Setting.py` 中刪除`csrf`中間件，而如果想對某些請求設置 `CSRF` 防護，那可以在模板文件中添加`{% csrf_token %}`，然後在對應視圖函數中添加裝飾器 `@csrf_protect`。
### 在 AJAX 請求中使用 CSRF token
如果你使用 JavaScript（如 jQuery、Axios）發送 AJAX 請求，也需要確保 CSRF token 被包含在請求中。Django 提供了一個方便的 JavaScript 函數來獲取 CSRF token，並將其附加到 AJAX 請求的標頭中。

首先，確保你的模板中包含 CSRF token 的值：

```python
<script type="text/javascript">
    function submitForm(){
	    varcsrf = $('input[name="csrfmiddlewaretoken"]').val():
	    var user = $('username').val();
	    var password = $('password').val();
	    $.ajax({
		    url:'/index.html',
		    type:'POST',
		    data:{
			    'user':user,
			    'password':password,
			    'csrfmiddlewaretoken':csrf,
			    'success':function(arg){
				    console.log(arg);
			    }
		    }
	    })
    }
</script>
```