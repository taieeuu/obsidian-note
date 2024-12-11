---
Date: 2024-08-22
---
中間件（Middleware）是 Django 應用處理過程中的一個鉤子，允許你在請求和回應之間執行自定義邏輯。Django 的中間件位於請求處理的不同階段，可以用來修改請求、添加額外的上下文信息、處理例外情況或對回應進行處理。
### 中間件的定義過程
中間件在 `settings.py` 的配置屬性 `MIDDLEWARE` 中進行設置。在創建項目時，Django 已默認配置了 7 個中間件。如下:

- `SecurityMiddleware` : 內置的安全機制，用於保護用戶與網站的通訊安全
- `SessionMiddleware` : 會話功能
-  `CommonMiddleware` : 處理請求訊息，規範化請求內容
-  `CsrfViewMiddleware` : 開啟 `CSRF` 防護功能
-  `AuthenticationMiddleware` : 開啟內置得用戶認證系統
-  `MessageMiddleware` : 開啟內置的訊息提示功能
-  `XFrameOptionsMiddleware` : 防止惡意程序點擊劫持
#### 中間件的基本流程如下：
- 請求進入 Django 時，請求會經過每個中間件的 `process_request` 方法。
- 請求被視圖處理後，回應會經過每個中間件的 `process_response` 方法。
#### 自定義中間件
要創建一個自定義中間件，你需要編寫一個 Python 類，並實現以下方法之一或多個：

- `__init__(self, get_response)`：初始化中間件，`get_response` 是 Django 傳遞的回應處理器。
- `__call__(self, request)`：每個請求都會調用此方法，它必須返回一個回應對象。
- `process_view(self, request, view_func, view_args, view_kwargs)`：在視圖被調用之前調用，可以用來修改視圖的行為。
- `process_exception(self, request, exception)`：當視圖引發異常時調用，可以用來處理異常並返回自定義回應。
- `process_template_response(self, request, response)`：在視圖返回一個 `TemplateResponse` 對象後調用，用於修改模板上下文。
- `process_request(self, request)` 和 `process_response(self, request, response)` 方法已不推薦使用，應該優先使用 `__call__` 方法。
#### 範例：請求計時中間件
以下是一個簡單的自定義中間件示例，用於記錄每個請求的處理時間。

```python
import time

class RequestTimingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # 請求處理前記錄時間
        start_time = time.time()

        # 處理請求並獲取回應
        response = self.get_response(request)

        # 請求處理後計算耗時
        duration = time.time() - start_time
        response['X-Request-Duration-ms'] = int(duration * 1000)  # 將耗時添加到回應頭中

        return response
```

這個中間件會在每個請求的回應頭中添加一個 `X-Request-Duration-ms` 字段，標註該請求的處理時間。

### 配置中間件
要啟用自定義中間件，你需要在 `settings.py` 中的 `MIDDLEWARE` 設置中註冊該中間件。

```python
# myproject/settings.py

MIDDLEWARE = [
    # 其他中間件
    'myapp.middleware.RequestTimingMiddleware',  # 註冊自定義中間件
    # 其他中間件
]
```

請注意，中間件的執行順序與 `MIDDLEWARE` 列表中的順序一致。
### 4. 中間件中的異常處理
你可以使用 `process_exception` 方法來處理中間件中的異常。當視圖代碼引發異常時，Django 會調用 `process_exception`，允許你返回自定義的錯誤頁面或進行日誌記錄。

```python
class ExceptionHandlingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        try:
            response = self.get_response(request)
        except Exception as e:
            return self.process_exception(request, e)
        return response

    def process_exception(self, request, exception):
        # 自定義的異常處理邏輯，例如記錄異常或返回錯誤頁面
        print(f"Exception occurred: {exception}")
        return HttpResponse("Something went wrong!", status=500)
```
### 實現 Cookie 反爬蟲
```python
from django.tuils.deprecation import MiddlewareMixin
from django.utils.timezone import now
from django.shortcuts import Http404
class MyMiddleware(MiddlewareMixin):
	def process_view(self,reuqest, func, args, kwargs):
		if func.__name__ != 'index':
			salt = request.COOKIES.get('value', '')
			try:
				request.get_signed_cookie('MySign', salt=salt)
			except Exception as e:
				print(e)
				raise Http404('當前Cookie無效')
	def process_response(self, request, response):
		salt = str(now())
		response.set_signed_cookie(
			'MySign',
			'sign',
			salt=salt,
			max_age=10
		)
		response.set_cookie('value', salt)
		return response
```

- **`process_view`** 方法會在 Django 處理視圖（View）之前被調用。
- 首先，它檢查當前要執行的視圖函數名是否不是 `'index'`。這意味著這個中間件只會對非 `index` 視圖的請求進行檢查。
- 接著，它從請求的 Cookie 中獲取名為 `'value'` 的 Cookie 值作為 `salt`。如果這個 Cookie 不存在，`salt` 的值會是一個空字符串。
- 然後，它嘗試通過 `request.get_signed_cookie` 方法來檢查名為 `'MySign'` 的簽名 Cookie。這個方法會驗證 Cookie 的簽名是否有效，並使用 `salt` 進行簽名的驗證。
- 如果簽名無效或出現其他異常，會引發一個 `Http404` 異常，並顯示 `'當前Cookie無效'` 的錯誤信息，這意味著該請求會被拒絕，並返回一個 404 錯誤頁面。

- **`process_response`** 方法在 Django 構建回應之後但返回給用戶之前被調用。
- 它的作用是設置新的簽名 Cookie 和普通 Cookie。
- 首先，它使用 `now()` 函數獲取當前時間並將其轉換為字符串形式，這個字符串被用作新的 `salt` 值。
- 然後，它設置一個名為 `'MySign'` 的簽名 Cookie，值為 `'sign'`，並使用新的 `salt` 進行簽名。這個 Cookie 的有效期為 10 秒 (`max_age=10`)。
- 同時，它還設置一個普通的名為 `'value'` 的 Cookie，存儲生成的 `salt` 值。
- 最後，它返回修改後的回應對象。