---
Date: 2024-08-18
---
`Permission` 模型表示具體的權限，如“可以添加用戶”、“可以更改用戶”等。這些權限可以直接分配給用戶，也可以通過群組來分配。
### 使用 `Permission` 模型進行權限控制
每個權限與一個特定的模型操作相關聯（添加、更改、刪除、查看）。Django 自動為每個模型生成基本的 CRUD（創建、讀取、更新、刪除）權限，但你也可以創建自定義權限。
##### 創建自定義權限
首先，在模型的 `Meta` 類中定義自定義權限：

```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=100)

    class Meta:
        permissions = [
            ('can_publish', 'Can Publish Book'),
        ]
```

這樣，你的應用將擁有一個名為 `can_publish` 的權限。
##### 分配權限給用戶
```python
from django.contrib.auth.models import Permission

permission = Permission.objects.get(codename='can_publish')
user.user_permissions.add(permission)
```
### 驗證用戶的權限
在視圖中，你可以使用 `has_perm` 方法來檢查用戶是否擁有某個權限。

```python
if user.has_perm('myapp.can_publish'):
    # 用戶具有發布權限
    pass
```
### 3.3 基於權限的視圖保護
Django 提供了裝飾器來保護基於權限的視圖，確保只有具備特定權限的用戶才能訪問。

```python
from django.contrib.auth.decorators import permission_required

@permission_required('myapp.can_publish')
def publish_book(request, book_id):
    # 僅具備 can_publish 權限的用戶才能訪問此視圖
    pass
```