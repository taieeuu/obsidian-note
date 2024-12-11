---
Date: 2024-08-18
---
Django 的認證後端負責處理用戶登錄過程中的驗證邏輯。默認情況下，Django 使用 `ModelBackend` 來通過用戶名和密碼進行驗證，但你可以自定義認證後端，以支持不同的認證方式，如電子郵件登錄、第三方 OAuth 認證等。
### 默認的認證後端
默認情況下，Django 使用 `ModelBackend` 進行認證。這個後端允許用戶通過用戶名和密碼登錄。
### 自定義認證後端
你可以創建自定義的認證後端，以便用戶使用其他憑據（如電子郵件或手機號）進行登錄。
##### 創建自定義認證後端
```python
from django.contrib.auth.backends import ModelBackend
from django.contrib.auth import get_user_model

class EmailBackend(ModelBackend):
    def authenticate(self, request, username=None, password=None, **kwargs):
        UserModel = get_user_model()
        try:
            user = UserModel.objects.get(email=username)
        except UserModel.DoesNotExist:
            return None

        if user.check_password(password):
            return user
        return None
```
##### 在 `settings.py` 中配置認證後端
```python
AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',  # 默認後端
    'myapp.backends.EmailBackend',  # 自定義後端
]
```
### 多重認證後端
你可以在 `settings.py` 中配置多個認證後端，Django 將按照定義的順序嘗試使用這些後端進行驗證。這允許用戶使用多種方式進行登錄，例如用戶名或電子郵件。

```python
AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',
    'myapp.backends.EmailBackend',
]
```

這樣，用戶可以通過用戶名或電子郵件進行登錄，具體取決於哪個認證後端首先成功驗證。