---
Date: 2024-08-18
---
### 認證（Authentication）
#### 認證的基本概念
在 DRF 中，認證是確定 API 請求者身份的過程。認證系統會將請求中的憑證（例如 token 或 session）與後端存儲的用戶數據進行匹配，從而確認請求者的身份。
#### 認證類型
DRF 支援多種認證方式，以下是最常用的幾種：
##### 基於 Session 的認證
基於 Session 的認證使用 Django 內建的 session 機制，適用於傳統的 Web 應用程序，特別是當 API 與 Django 的模板視圖混合使用時。
##### Token 認證
Token 認證是 API 認證的一種常見方式，每個用戶對應一個唯一的 token。當用戶登錄後，伺服器會生成一個 token 並返回給用戶，隨後用戶每次請求 API 時，會在請求頭中附帶這個 token 進行認證。
##### JWT 認證
JWT（JSON Web Token）是一種更加輕量的 token 認證方式，它不需要在伺服器端存儲 token，而是將用戶信息加密後生成一個 token，並在每次請求中攜帶這個 token 進行認證。JWT 具有跨域和無狀態的特點，適合前後端分離的應用。
#### 配置認證方式
在 DRF 中，可以通過 `settings.py` 配置使用的認證類型。
###### 設定為使用 Token 認證
首先，確保安裝了 `djangorestframework.authtoken` 應用：

```python
INSTALLED_APPS = [
    'rest_framework',
    'rest_framework.authtoken',
    ...
]
```
#### Token 認證的使用
在設置了 Token 認證後，用戶需要使用 `Token` 來訪問受保護的 API。
##### 生成 Token
當用戶成功登錄後，伺服器生成並返回一個 Token。這可以通過 Django 管理介面或命令生成：

```python
from rest_framework.authtoken.models import Token
from django.contrib.auth.models import User

user = User.objects.get(username='john')
token = Token.objects.create(user=user)
print(token.key)
```
##### 在 API 視圖中使用認證
在視圖中，使用 `authentication_classes` 指定要使用的認證類型，並使用 `permission_classes` 設定權限：

```python
from rest_framework.views import APIView
from rest_framework.authentication import TokenAuthentication
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response

class ExampleView(APIView):
    authentication_classes = [TokenAuthentication]
    permission_classes = [IsAuthenticated]

    def get(self, request):
        content = {'message': '這是一個受保護的 API！'}
        return Response(content)
```
#### JWT 認證的使用
JWT 認證通常需要使用第三方套件，如 `djangorestframework-simplejwt`。
##### 安裝 JWT 認證套件
```sh
pip install djangorestframework-simplejwt
```
##### 配置 JWT 認證
在 `settings.py` 中配置 JWT 認證：

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}
```
#### 生成和驗證 JWT
使用 JWT 認證時，用戶可以通過提供用戶名和密碼來獲取 token，並在後續請求中附帶這個 token 進行認證。

```python
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView
from django.urls import path

urlpatterns = [
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]
```
### 2. 權限（Permissions）

#### 2.1 權限的基本概念

在確認了用戶身份之後，權限系統會確定用戶是否有權執行特定操作或訪問特定資源。權限檢查是在視圖的 `has_permission` 和 `has_object_permission` 方法中進行的。

#### 內建的權限類型
DRF 提供了多種內建的權限類型，滿足常見的需求：
##### IsAuthenticated
`IsAuthenticated` 確保只有已認證的用戶才能訪問視圖。如果用戶未認證，則會返回 401 Unauthorized 錯誤。
##### IsAdminUser
`IsAdminUser` 確保只有管理員用戶才能訪問視圖。如果用戶不是管理員，則會返回 403 Forbidden 錯誤。
##### IsAuthenticatedOrReadOnly
`IsAuthenticatedOrReadOnly` 確保已認證的用戶可以執行所有操作，而未認證的用戶只能進行安全的「讀」操作（如 GET 請求）。
#### 在視圖中使用權限
可以在視圖中設置 `permission_classes` 來指定視圖的權限要求。
##### 使用示例
```python
from rest_framework.permissions import IsAuthenticatedOrReadOnly
from rest_framework.views import APIView
from rest_framework.response import Response

class ArticleView(APIView):
    permission_classes = [IsAuthenticatedOrReadOnly]

    def get(self, request):
        return Response({"message": "任何人都可以查看"})

    def post(self, request):
        return Response({"message": "只有認證用戶可以創建"})
```
#### 自定義權限
當內建權限無法滿足需求時，可以通過自定義權限類來實現複雜的邏輯。
##### 自定義權限示例
```python
from rest_framework.permissions import BasePermission

class IsAuthorOrReadOnly(BasePermission):
    def has_object_permission(self, request, view, obj):
        # 讀操作允許所有用戶訪問
        if request.method in ['GET', 'HEAD', 'OPTIONS']:
            return True
        # 只有文章作者才能修改或刪除
        return obj.author == request.user
```

將自定義權限應用於視圖：

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from .permissions import IsAuthorOrReadOnly
from .models import Article

class ArticleDetailView(APIView):
    permission_classes = [IsAuthorOrReadOnly]

    def get(self, request, pk):
        article = get_object_or_404(Article, pk=pk)
        return Response({"title": article.title, "content": article.content})

    def delete(self, request, pk):
        article = get_object_or_404(Article, pk=pk)
        article.delete()
        return Response(status=204)
```