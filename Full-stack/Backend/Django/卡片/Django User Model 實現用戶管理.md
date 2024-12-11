---
Date: 2024-08-18
---
Django 的 `User` 模型是認證系統的核心，負責表示應用中的用戶信息。它包括了用戶名、密碼、電子郵件、名字、姓氏等基本屬性，並且支持許多與用戶相關的操作，如驗證、權限管理等。
### `User` 模型的基本結構
`User` 模型包含以下主要字段：

- **`username`**: 用戶名，是唯一標識一個用戶的關鍵字段。
- **`email`**: 用戶的電子郵件地址。
- **`password`**: 用戶的哈希密碼，Django 使用安全的哈希算法來存儲密碼。
- **`first_name`** 和 **`last_name`**: 用戶的名字和姓氏。
- **`is_active`**: 布爾值，表示用戶是否處於活躍狀態。非活躍用戶無法登錄。
- **`is_staff`**: 布爾值，表示用戶是否有權訪問 Django 管理後台。
- **`is_superuser`**: 布爾值，表示用戶是否具有所有權限。
- **`last_login`**: 用戶的最後登錄時間。
- **`date_joined`**: 用戶註冊時間。
### 用戶的創建與管理
你可以使用 Django 提供的 API 來創建和管理 `User` 對象。
##### 創建新用戶
```python
from django.contrib.auth.models import User

# 創建用戶
user = User.objects.create_user(username='john', email='john@example.com', password='password123')

# 設置額外屬性
user.first_name = 'John'
user.last_name = 'Doe'
user.save()
```
##### 用戶認證
Django 提供了簡單的方式來驗證用戶的憑據。

```python
from django.contrib.auth import authenticate, login

user = authenticate(username='john', password='password123')
if user is not None:
    login(request, user)
else:
    # 憑據無效，處理錯誤
    pass
```
##### 用戶登出
```python
from django.contrib.auth import logout

def logout_view(request):
    logout(request)
    return redirect('home')
```
### 自定義 `User` 模型
在一些應用中，默認的 `User` 模型可能無法滿足需求，這時你可以創建自定義用戶模型來擴展或替換它。

```python
from django.contrib.auth.models import AbstractUser

class CustomUser(AbstractUser):
    phone_number = models.CharField(max_length=15, blank=True)
```

記得在 `settings.py` 中指定使用自定義的用戶模型：

```python
AUTH_USER_MODEL = 'myapp.CustomUser'
```