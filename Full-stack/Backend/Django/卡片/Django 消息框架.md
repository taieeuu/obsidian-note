---
Date: 2024-08-24
---
jango 的消息框架（Message Framework）是一個用來在請求之間臨時存儲和顯示消息的工具。這些消息通常用於向用戶傳達一些狀態信息，例如操作成功、錯誤提示或警告。消息框架將這些消息保存在會話（session）中或其他存儲後端中，並在下一個請求中顯示給用戶。

Django 消息框架的工作方式是將消息附加到當前的 HTTP 請求中，然後在下一個請求中顯示這些消息。這使得你可以在處理某個操作（例如表單提交）後向用戶顯示反饋信息。
### 消息的類型
- **`messages.debug`**: 診斷信息，通常在開發階段使用。
- **`messages.info`**: 一般信息，告知用戶某些普通事件或狀態。
- **`messages.success`**: 成功信息，告知用戶某些操作成功完成。
- **`messages.warning`**: 警告信息，告知用戶需要注意的事項。
- **`messages.error`**: 錯誤信息，告知用戶發生了某些錯誤。
### 使用消息框架
#### 在視圖中添加消息
在視圖中，你可以使用 `django.contrib.messages` 模塊來添加消息。通常會使用像 `messages.success()` 或 `messages.error()` 這樣的快捷方法。

```python
from django.contrib import messages
from django.shortcuts import render, redirect

def my_view(request):
    # 處理一些業務邏輯
    messages.success(request, "操作成功！")
    messages.error(request, "發生了一個錯誤。")

    return redirect('some_view_name')
```
#### 在模板中顯示消息
要在模板中顯示這些消息，你需要迭代 `messages` 上下文變量。Django 的默認 `messages` 中間件會自動將消息傳遞給模板。

首先，確保在模板中載入消息框架的標籤庫：

```django html
{% load messages %}
```

然後，通過迭代 `messages` 變量來顯示所有的消息：

```django html
<ul>
    {% for message in messages %}
        <li{% if message.tags %} class="{{ message.tags }}"{% endif %}>
            {{ message }}
        </li>
    {% endfor %}
</ul>
```

這個代碼會遍歷所有的消息，並根據消息的類型（如 `success`、`error` 等）添加對應的 CSS 樣式類別（`message.tags`）。
#### 配置消息存儲後端
Django 默認使用基於 session 的消息存儲後端。你可以在 `settings.py` 中配置其他存儲後端，比如基於 cookie 的存儲後端。

```python
# settings.py

from django.contrib.messages import constants as message_constants

# 消息存儲後端配置
MESSAGE_STORAGE = 'django.contrib.messages.storage.session.SessionStorage'

# 自定義消息標籤（可選）
MESSAGE_TAGS = {
    message_constants.DEBUG: 'debug',
    message_constants.INFO: 'info',
    message_constants.SUCCESS: 'success',
    message_constants.WARNING: 'warning',
    message_constants.ERROR: 'danger',  # 可以用 'danger' 來代替 'error'，以適應 Bootstrap 樣式
}
```
#### 清除消息
消息在被顯示後會自動從存儲中刪除。如果你需要在特定情況下手動清除消息，可以使用 `messages.clear()` 方法：

```python
from django.contrib import messages

# 清除所有消息
messages.clear(request)
```
#### 定義自定義消息級別
除了內置的消息類型，你還可以定義自定義的消息級別。這可以讓你更加靈活地管理和顯示不同類型的消息。

```python
from django.contrib import messages

# 自定義一個新的消息級別
MY_CUSTOM_LEVEL = 25  # 自定義的數字，介於內置消息級別之間
messages.add_level(request, MY_CUSTOM_LEVEL, "這是自定義消息。", extra_tags='custom_tag')
```
#### 常見應用場景
- **表單提交後的反饋**：在用戶提交表單後向他們顯示成功或錯誤消息。
- **權限檢查**：在用戶無權訪問某些頁面時顯示警告或錯誤消息。
- **操作通知**：在執行某些操作後向用戶通知結果，例如刪除數據或更新設置。