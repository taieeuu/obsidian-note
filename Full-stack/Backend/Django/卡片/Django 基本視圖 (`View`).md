---
Date: 2024-08-17
---
在 Django 中，`View` 是最基本的視圖類，它提供了處理 HTTP 請求的基礎結構。所有的基於類的視圖（Class-Based Views，CBV）都繼承自 `View` 類或其子類。
### View 的作用
`View` 類的主要作用是將 HTTP 請求映射到特定的處理方法。每個 HTTP 方法（如 GET、POST、PUT、DELETE 等）都有相應的處理方法（如 `get()`、`post()` 等），當一個 HTTP 請求到達時，Django 會將這個請求分派給對應的方法來處理。
### View 的基本結構
```python
from django.views import View
from django.http import HttpResponse

class MyView(View):
    def get(self, request, *args, **kwargs):
        return HttpResponse('Hello, World!')

    def post(self, request, *args, **kwargs):
        return HttpResponse('This is a POST request')
```
### `View` 的核心概念
1. **HTTP 方法處理**: 每個 `View` 子類通常會實現一個或多個 HTTP 方法的處理方法，如 `get()`、`post()` 等。這些方法接受一個 `HttpRequest` 對象，並返回一個 `HttpResponse` 對象。
   
2. **`as_view()` 方法**: Django 的 `View` 類提供了一個名為 `as_view()` 的類方法，用於將視圖類轉換為一個可調用的視圖函數。這個視圖函數會在 URLconf 中被使用。`as_view()` 方法會實例化視圖類，並調用對應的 HTTP 方法來處理請求。

	```python
	from django.urls import path
	from .views import MyView
	
	urlpatterns = [
	    path('myview/', MyView.as_view(), name='my_view'),
	]
	```

3. **Mixin 支持**: `View` 類通常與 Mixin 類一起使用，以便擴展視圖的功能。例如，`TemplateView` 就是 `View` 與模板渲染功能的 Mixin 的結合。
    
4. **Dispatch 方法**: `View` 類中還有一個重要的 `dispatch()` 方法，這個方法負責接收 HTTP 請求，並根據請求方法（GET、POST 等）分派給對應的處理方法（`get()`、`post()` 等）。如果一個視圖類中沒有實現該 HTTP 方法的處理方法，則會返回 HTTP 405（Method Not Allowed）錯誤。
   
5. **擴展性**: 由於 `View` 是所有基於類的視圖的基類，開發者可以根據需要自定義和擴展 `View` 類，以實現特定的業務邏輯或行為。