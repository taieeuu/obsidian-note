---
Date: 2024-08-18
---
### 路由器的基本概念
路由器（Routers）是 Django Rest Framework 中的工具，用於將 `ViewSet` 中定義的動作（如列表、創建、更新、刪除）自動映射到 URL 模式。路由器的主要作用是簡化 URL 配置，避免手動編寫大量重複的 URL 路徑。
#### 路由器的作用
- **自動生成 URL 模式**：路由器根據 `ViewSet` 中的動作自動生成相應的 URL 路徑。
- **簡化 URL 配置**：路由器消除了手動配置每個 CRUD 操作的 URL 模式的需求，減少了錯誤並提高了開發效率。
### 路由器類型
Django Rest Framework 提供了多種路由器類型，每一種都針對不同的應用場景進行了優化。
#### DefaultRouter
`DefaultRouter` 是 DRF 中最常用的路由器類型。它不僅自動生成標準的 CRUD URL 模式，還提供了一個默認的 API 根視圖（API root view），這個根視圖會顯示所有註冊的 `ViewSet` 的入口點。
##### 使用示例
```python
from rest_framework.routers import DefaultRouter
from .views import ArticleViewSet

router = DefaultRouter()
router.register(r'articles', ArticleViewSet)

urlpatterns = router.urls
```

這個配置會生成如下的 URL 模式：

- `GET /articles/` - 列出所有文章
- `POST /articles/` - 創建新文章
- `GET /articles/{pk}/` - 檢索特定文章
- `PUT /articles/{pk}/` - 更新特定文章
- `PATCH /articles/{pk}/` - 部分更新特定文章
- `DELETE /articles/{pk}/` - 刪除特定文章

同時，`DefaultRouter` 還會生成一個 API 根視圖（默認在 `/` 路徑下），列出所有已註冊的視圖集。
#### SimpleRouter
`SimpleRouter` 與 `DefaultRouter` 類似，但它不會生成 API 根視圖。如果你不需要這個根視圖，`SimpleRouter` 會是一個更加輕量的選擇。
##### 使用示例
```python
from rest_framework.routers import SimpleRouter
from .views import ArticleViewSet

router = SimpleRouter()
router.register(r'articles', ArticleViewSet)

urlpatterns = router.urls
```

生成的 URL 模式與 `DefaultRouter` 類似，但不包含 API 根視圖。
#### Custom Routers
在某些情況下，你可能需要自定義路由器來滿足特定的需求。DRF 允許你通過繼承 `SimpleRouter` 或 `DefaultRouter` 來創建自定義路由器。
##### 自定義路由器示例
```python
from rest_framework.routers import SimpleRouter

class CustomRouter(SimpleRouter):
    def get_default_basename(self, viewset):
        # 自定義基礎名稱生成邏輯
        return super().get_default_basename(viewset) + '_custom'

router = CustomRouter()
router.register(r'articles', ArticleViewSet)

urlpatterns = router.urls
```

這樣的自定義路由器允許你更靈活地控制 URL 模式的生成。
####  Nested Routers
如果你有一個嵌套的資源結構，`drf-nested-routers` 是一個很有用的第三方包，它允許你創建嵌套的路由。
##### 安裝 drf-nested-routers
首先，你需要安裝這個包：

```python
pip install drf-nested-routers
```
##### 使用示例
```python
from rest_framework_nested.routers import DefaultRouter, NestedDefaultRouter
from .views import AuthorViewSet, ArticleViewSet

router = DefaultRouter()
router.register(r'authors', AuthorViewSet)

articles_router = NestedDefaultRouter(router, r'authors', lookup='author')
articles_router.register(r'articles', ArticleViewSet, basename='author-articles')

urlpatterns = router.urls + articles_router.urls
```
\
這樣配置會生成如下的 URL 模式：

- `GET /authors/{author_pk}/articles/` - 列出特定作者的所有文章
- `POST /authors/{author_pk}/articles/` - 為特定作者創建新文章
- `GET /authors/{author_pk}/articles/{pk}/` - 檢索特定文章
- `PUT /authors/{author_pk}/articles/{pk}/` - 更新特定文章
- `DELETE /authors/{author_pk}/articles/{pk}/` - 刪除特定文章

這種嵌套路由對於表現資源之間的父子關係特別有用。
### 3. 路由器的應用場景
#### 使用 `DefaultRouter` 構建标准 API
在需要快速構建標準 RESTful API 的场景下，`DefaultRouter` 是理想选择。它不僅簡化了 URL 的配置，还提供了 API 根视图，帮助开发者更好地组织和查看 API 入口。
#### 使用 `SimpleRouter` 進行精简配置
如果项目中不需要 API 根視圖，或者希望保留 API 路徑的完全控制權，`SimpleRouter` 是更加合适的选择。它提供了与 `DefaultRouter` 类似的自动 URL 生成功能，但更輕量。
#### 使用 `Custom Routers` 時線特殊需求
在一些复杂项目中，你可能需要自定义路由器的行为，例如自定义路径生成规则、引入额外的 URL 参数等。这时候，自定义路由器可以满足你的特殊需求。
#### 使用 `Nested Routers` 構建嵌套资源 API
在设计具有父子关系的 API 结构时，例如用户和订单、博客和评论，`Nested Routers` 非常有用。它能够清晰地反映资源之间的嵌套关系，方便客户端和服务器之间的数据交互。