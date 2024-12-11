---
Date: 2024-08-18
---
在 DRF 中，視圖是用來處理 HTTP 請求的邏輯單元。視圖負責接收請求、調用相應的業務邏輯（如與模型交互、使用序列化器處理數據）、並最終返回響應。DRF 擴展了 Django 的視圖功能，提供了針對 API 開發的多種視圖類型。
### 視圖類型
DRF 提供了多種視圖類型，從簡單到複雜，適用於不同的場景和需求。
#### ### APIView
`APIView` 是 DRF 中最基本的視圖類型，它直接繼承自 Django 的 `View` 類，並進行了一些擴展以支持 API 開發。使用 `APIView` 時，你可以針對不同的 HTTP 方法（如 GET、POST、PUT、DELETE）來定義處理邏輯。
##### 基本示例
```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Article
from .serializers import ArticleSerializer

class ArticleList(APIView):
    def get(self, request):
        articles = Article.objects.all()
        serializer = ArticleSerializer(articles, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = ArticleSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```
###### 特點
- **靈活性高**：你可以完全控制每個 HTTP 方法的處理邏輯。
- **手動處理 HTTP 方法**：需要手動定義每個 HTTP 方法的處理器，如 `get()`、`post()` 等。
#### GenericAPIView
`GenericAPIView` 是一個比 `APIView` 更加靈活的基類，它提供了許多與通用視圖功能相關的功能，例如查詢集、序列化器、分頁和過濾等。這使得你可以更輕鬆地構建基於模型的 API 視圖。
##### 基本示例
```python
from rest_framework.generics import GenericAPIView
from rest_framework.mixins import ListModelMixin, CreateModelMixin
from .models import Article
from .serializers import ArticleSerializer

class ArticleList(GenericAPIView, ListModelMixin, CreateModelMixin):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)
```
###### 特點
- **通用功能**：提供了通用功能，如 `queryset` 和 `serializer_class`，使得代碼更加簡潔。
- **與 Mixin 結合使用**：通過與 Mixin 結合，可以實現列表、創建、更新、刪除等功能，而無需重複代碼。
#### Mixins
Mixins 是一組可以與 `GenericAPIView` 結合使用的類，用於實現常見的操作，如列表顯示、創建、更新和刪除。DRF 提供了一系列 Mixin 類，如 `ListModelMixin`、`CreateModelMixin`、`UpdateModelMixin` 和 `DestroyModelMixin`。
##### 基本使用示例
```python
from rest_framework.generics import GenericAPIView
from rest_framework.mixins import RetrieveModelMixin, UpdateModelMixin, DestroyModelMixin
from .models import Article
from .serializers import ArticleSerializer

class ArticleDetail(GenericAPIView, RetrieveModelMixin, UpdateModelMixin, DestroyModelMixin):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)
```
###### 特點
- **代碼重用性高**：通過 Mixins，你可以重用很多常見操作的代碼。
- **簡化視圖邏輯**：Mixins 將常見的 CRUD 操作封裝起來，讓你可以更專注於定制化邏輯。
#### Generic Views
DRF 提供了一組基於 `GenericAPIView` 的具體類視圖，這些視圖已經內置了常見的 CRUD 功能，如 `ListAPIView`、`CreateAPIView`、`RetrieveAPIView` 等，讓你可以更快速地構建 API。
##### 基本使用示例
```python
from rest_framework.generics import ListCreateAPIView, RetrieveUpdateDestroyAPIView
from .models import Article
from .serializers import ArticleSerializer

class ArticleList(ListCreateAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

class ArticleDetail(RetrieveUpdateDestroyAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```
###### 特點
- **簡單易用**：提供了開箱即用的 CRUD 操作視圖，無需手動定義每個 HTTP 方法。
- **可擴展性**：可以通過繼承和重寫方法來擴展這些視圖的行為。
#### ViewSet
`ViewSet` 是 DRF 提供的高級視圖類，將常見的視圖操作（如列表顯示、創建、檢索、更新和刪除）集中在一個類中。`ViewSet` 通常與路由器（Routers）結合使用，根據路由自動調用對應的方法。
##### 基本使用示例
```python
from rest_framework import viewsets
from .models import Article
from .serializers import ArticleSerializer

class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```

在 `urls.py` 中：

```python
from rest_framework.routers import DefaultRouter
from .views import ArticleViewSet

router = DefaultRouter()
router.register(r'articles', ArticleViewSet)

urlpatterns = router.urls
```
###### 特點
- **集中管理**：將所有與資源相關的操作集中在一個類中，減少代碼重複。
- **與路由器結合使用**：路由器可以自動處理 URL 和視圖方法的映射，簡化了路由配置。
#### Routers（路由器）
路由器（Routers）是與 `ViewSet` 結合使用的工具，用於自動生成對應的 URL 模式。DRF 提供了多種路由器類型，如 `DefaultRouter` 和 `SimpleRouter`，它們可以自動將 URL 映射到 `ViewSet` 的方法上。
##### 基本使用示例
```python
from rest_framework.routers import DefaultRouter
from .views import ArticleViewSet

router = DefaultRouter()
router.register(r'articles', ArticleViewSet)

urlpatterns = router.urls
```
###### 特點
- **自動路由**：路由器自動生成與 `ViewSet` 操作對應的 URL，減少手動配置的工作量。
- **簡化 URL 配置**：減少了配置 URL 模式的重複工作。
### 視圖的應用場景
##### 小型項目
在小型項目中，可能更傾向於使用 `APIView` 或 `GenericAPIView` 來完全控制視圖的邏輯，這樣可以根據具體需求靈活定制每個視圖的行為。
##### 中型項目
在中型項目中，使用 Mixins 或基於 `GenericAPIView` 的具體類視圖（如 `ListCreateAPIView`、`RetrieveUpdateDestroyAPIView`）能夠幫助你快速構建標準的 CRUD API，並保持代碼簡潔。
##### 大型項目
在大型項目中，`ViewSet` 與路由器的組合是理想選擇。它們能夠將所有與特定資源相關的操作集中在一個類中，並且通過自動生成的路由器來簡化 URL 配置。
#####  當需要高度定制時
如果你需要針對不同的 HTTP 方法進行高度定制，或需要處理複雜的邏輯，使用 `APIView` 或 `GenericAPIView` 是更好的選擇。