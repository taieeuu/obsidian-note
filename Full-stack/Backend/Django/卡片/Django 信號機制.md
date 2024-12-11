---
Date: 2024-08-24
---
Django 的信號機制是一種允許不同部分的代碼以解耦方式進行通信的系統。通過信號，Django 應用中的某些事件發生時，可以自動觸發其他代碼的執行，而無需顯式調用它們。這在處理事件驅動的任務（如數據庫模型的保存、刪除等操作）時非常有用。
### 信號的基本概念
信號是一種消息傳遞系統，它包括以下幾個組件：

- **信號（Signal）**：信號是一個具體的事件或消息，可以被“發送”或“接收”。
- **發送者（Sender）**：信號的發送者，通常是觸發事件的對象或模塊。
- **接收器（Receiver）**：接收信號並執行某些操作的函數或方法。

Django 提供了一些內置的信號，例如 `pre_save`、`post_save`、`pre_delete` 和 `post_delete`，這些信號與模型的保存和刪除操作有關。詳細可參考 : [官方文檔](https://docs.djangoproject.com/zh-hans/5.1/ref/signals/)
### 使用信號的步驟
使用 Django 的信號機制主要包括以下幾個步驟：

1. **導入信號和信號處理器（接收器）裝飾器**。
2. **定義信號接收器**，即當信號觸發時需要執行的函數。
3. **將接收器連接到信號**，通常通過裝飾器或顯式的連接函數來實現。
###### 示例：在模型保存後執行某些操作
假設我們有一個 `BlogPost` 模型，我們希望在每次保存這個模型後執行某些操作，比如打印一條信息或更新其他相關模型。

```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import BlogPost

@receiver(post_save, sender=BlogPost)
def my_handler(sender, instance, created, **kwargs):
    if created:
        print(f"A new blog post '{instance.title}' was created!")
    else:
        print(f"The blog post '{instance.title}' was updated.")
```

- **`post_save`** 是一個內置的信號，表示模型對象在保存後觸發。
- **`@receiver(post_save, sender=BlogPost)`** 是一個裝飾器，用於將 `my_handler` 函數連接到 `post_save` 信號，並指定發送者是 `BlogPost` 模型。
- **`my_handler`** 函數是信號的接收器，它會在 `BlogPost` 保存後自動執行。
### 常用內置信號
- **`pre_save`** 和 **`post_save`**：在模型對象保存之前和之後發送。
- **`pre_delete`** 和 **`post_delete`**：在模型對象刪除之前和之後發送。
- **`m2m_changed`**：當多對多關係改變時發送（如添加或刪除關聯對象）。
- **`request_started`** 和 **`request_finished`**：在請求開始和結束時發送。
- **`pre_migrate`** 和 **`post_migrate`**：在遷移操作之前和之後發送。

使用信號機制，有兩種方式，如下 :

```python
# 方法一，使用connect()
from django .core.signals import request_started

def signal_request(sender, **kwargs):
	print('request is cominng')
	print(sender)
	print(kwargs)
request_started.connect(signal_request)

# 方法二，使用裝飾器
from django.core.dispatch import receiver

@receiver(request_started)
def signal_request_2(sender, **kwargs):
	print('request is coming too')
	print(sender)
	print(kwargs)
```
### 自定義信號
除了內置信號外，Django 還允許你創建自定義信號。這在需要觸發特定事件時非常有用。記得最後在需要的地方，**發送信號**。
#### 自定義信號的例子
##### 定義自定義信號
在應用的 `signals.py` 文件中定義自定義信號：

```python
# myapp/signals.py
from django.dispatch import Signal

# 定義自定義信號
user_logged_in_signal = Signal(providing_args=["user", "timestamp"])
```
##### 定義接收器
定義一個接收器來處理這個信號，可以放在 `signals.py` 或者 `handlers.py` 中：

```python
# myapp/handlers.py
from django.dispatch import receiver
from .signals import user_logged_in_signal

@receiver(user_logged_in_signal)
def handle_user_logged_in(sender, **kwargs):
    user = kwargs.get('user')
    timestamp = kwargs.get('timestamp')
    print(f"User {user} logged in at {timestamp}.")
```
##### 在應用程序中連接信號
你可以在 `apps.py` 的 `ready` 方法中連接信號，以確保在應用啟動時就已經連接好了：

```python
# myapp/apps.py
from django.apps import AppConfig

class MyAppConfig(AppConfig):
    name = 'myapp'

    def ready(self):
        # 確保接收器被導入
        import myapp.handlers
```
##### 發送信號
在應用的某個地方（如視圖或其他邏輯中）發送信號，這會觸發接收器的執行：

```python
# myapp/views.py
from django.utils import timezone
from .signals import user_logged_in_signal

def user_login_view(request):
    user = request.user
    timestamp = timezone.now()

    # 用戶成功登錄後發送信號
    user_logged_in_signal.send(sender=None, user=user, timestamp=timestamp)

    # 繼續處理視圖邏輯
    ...
```

接著，啟動 Django 應用並執行登錄操作，接收器應該會自動被觸發並打印出用戶登錄的時間。
### 信號的應用場景
信號機制在 Django 中非常有用，特別適合以下場景：

- **跨模塊通信**：讓不同應用或模塊之間進行通信而無需直接依賴。
- **模型事件處理**：在模型保存、刪除等事件發生時自動觸發一些業務邏輯。
- **監控和日誌記錄**：捕獲特定事件並記錄到日誌中，方便監控系統運行狀況。
#### 實際應用場景
- 當用戶登入成功後，給該用戶發送通知消息。
- 如果網站設有緩存功能，當數據庫的數據發生變化時，緩存數據數量應減去訂單中的購買數量。
- 訂單狀態對庫存得影響。
### 注意事項
- **性能考量**：由於信號是同步執行的，如果接收器處理時間過長，可能會影響系統性能。因此，對於需要大量計算或耗時操作的任務，可以考慮將其放到後台任務中執行（如使用 Celery）。
- **依賴順序**：信號接收器的執行順序是根據它們連接的順序來確定的。如果有多個接收器對同一信號感興趣，需注意它們的執行順序。