---
Date: 2024-08-18
---
`Group` 模型允許你將用戶組織到群組中，每個群組可以分配一組權限。這樣，你可以更方便地管理和授權大批量的用戶。
### 2.1 `Group` 的基本使用
群組是一個集合，用於管理一組具有相同權限的用戶。你可以創建一個群組，然後將用戶添加到這個群組中。用戶隨即會繼承這個群組的所有權限。
##### 創建群組並分配權限
```python
from django.contrib.auth.models import Group, Permission

# 創建新群組
group = Group.objects.create(name='Editors')

# 添加權限到群組
permission = Permission.objects.get(codename='can_add_user')
group.permissions.add(permission)
```
##### 將用戶添加到群組
```python
from django.contrib.auth.models import User

user = User.objects.get(username='john')
user.groups.add(group)
```

這樣，`john` 將繼承 `Editors` 群組中的所有權限。
### 群組的應用場景
- **權限管理**：通過群組，你可以更有效地管理多個用戶的權限，而不是逐一分配。
- **角色分配**：例如，你可以創建“管理員”、“編輯”、“訪客”等群組，並為每個群組分配不同的權限。