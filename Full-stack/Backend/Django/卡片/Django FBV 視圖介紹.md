---
Date: 2024-08-25
---
基於函數的視圖（FBV）本質上是一個 Python 函數，該函數接受一個 `HttpRequest` 對象作為參數，並返回一個 `HttpResponse` 對象。這種視圖方式非常直觀且靈活，允許開發者精確控制視圖的邏輯。
### 基本結構
```python
from django.http import HttpResponse

def my_view(request):
    return HttpResponse("Hello, World!")
```

在這個例子中，`my_view` 是一個視圖函數，它接受一個 `request` 對象並返回一個包含字符串 "Hello, World!" 的 `HttpResponse` 對象。
#### 優點：
- **簡單直觀**：對於簡單的邏輯來說，FBV非常容易理解和實現。
- **可讀性高**：由於是使用純函式，所以程式碼非常易讀。
#### 缺點：
- **不適合複雜邏輯**：當視圖邏輯變得複雜時，FBV可能變得難以維護。
- **擴展性差**：相較於類型視圖（Class-Based View, CBV），FBV在需要增加額外功能時可能顯得不夠靈活。