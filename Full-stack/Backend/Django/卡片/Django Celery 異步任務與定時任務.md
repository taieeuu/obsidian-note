---
Date: 2024-08-20
---
Django 主要在視圖中執行，視圖是一個函數或類，而且視眈現成執行的，然而，在視圖函數或是圖類處理用戶請求時，複雜的數據讀寫操作或密集的計算往往會使響應時間過長，從而導致網頁卡死，影響用戶體驗。為了解決這類問題，我們可以在視圖中加入異步任務，讓她處理一些耗時的業務流程。
### 為什麼使用 Celery？
Celery 是一個分散式任務佇列系統，它非常適合處理耗時操作，這些操作不適合在請求-回應循環中進行。常見的使用場景包括：

- 發送電子郵件
- 數據處理
- 調用外部 API
- 定時操作，例如每天執行一次的報告生成

使用 Celery，可以將這些操作移至背景執行，避免阻塞用戶的請求，並且允許這些任務在預定時間或間隔內自動執行。
### 安裝
確保已在你的 Django 專案中安裝了 Celery 和 Redis。Redis 是一個流行的訊息代理，Celery 會通過它來傳遞任務。

```sh
pip install celery redis
```
### 配置 Celery
接著，我們需要在 Django 專案中配置 Celery。通常在專案的根目錄（與 `settings.py` 同級）下創建一個 `celery.py` 文件來完成這個配置。

**celery.py 文件**

```python
from __future__ import absolute_import, unicode_literals
import os
from celery import Celery

# 設置 Django 的設定模組為預設配置
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

app = Celery('myproject')

# 使用 Django 的 settings.py 配置
app.config_from_object('django.conf:settings', namespace='CELERY')

# 自動發現 tasks.py 文件中的任務
app.autodiscover_tasks()

@app.task(bind=True)
def debug_task(self):
    print(f'Request: {self.request!r}')
```
###### 在 `__init__.py` 文件中導入 Celery
為了確保 Django 能夠在專案啟動時加載 Celery，我們需要在專案的 `__init__.py` 文件中導入 Celery：

```python
from __future__ import absolute_import, unicode_literals
from .celery import app as celery_app

__all__ = ('celery_app',)
```
### 配置 Celery 設定
在 `settings.py` 中添加 Celery 的配置。這裡以使用 Redis 作為訊息代理為例。

```python
# myproject/settings.py

CELERY_BROKER_URL = 'redis://localhost:6379/0'
CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
CELERY_ACCEPT_CONTENT = ['json']
CELERY_TASK_SERIALIZER = 'json'
CELERY_RESULT_SERIALIZER = 'json'
CELERY_TIMEZONE = 'Asia/Taipei'
```
### 創建異步任務
在 Django 的應用程式中創建 `tasks.py` 文件來定義 Celery 任務。這些任務可以在背景執行，不會阻塞主線程。

```python
# myapp/tasks.py

from celery import shared_task

@shared_task
def add(x, y):
    return x + y

@shared_task
def send_email_task(email_address):
    # 假設這裡有一個發送電子郵件的函數
    send_email(email_address)
    return 'Email sent'
```

這些任務可以通過 Celery 在 Django 的視圖中觸發，比如：

```python
# myapp/views.py

from .tasks import add, send_email_task

def my_view(request):
    # 立即觸發異步任務
    result = add.delay(10, 20)
    
    # 觸發發送電子郵件任務
    send_email_task.delay('user@example.com')
    
    return HttpResponse('Tasks are being processed in the background')
```
### 運行 Celery Worker
在終端中運行 Celery worker，這樣 Celery 就可以開始處理異步任務：

```python
celery -A myproject worker -l info
```
### 設置定時任務
定時任務（Periodic Tasks）允許你按照預定的時間表運行任務。Celery 提供了 Celery Beat 來管理這些任務的排程。
###### 在 `celery.py` 中配置定時任務
```python
from celery.schedules import crontab

app.conf.beat_schedule = {
    'add-every-30-seconds': {
        'task': 'myapp.tasks.add',
        'schedule': 30.0,
        'args': (16, 16),
    },
    'send-email-every-monday-morning': {
        'task': 'myapp.tasks.send_email_task',
        'schedule': crontab(hour=7, minute=30, day_of_week=1),
        'args': ('user@example.com',),
    },
}
```
###### 運行 Celery Beat
運行 Celery Beat 來開始排程定時任務：

```python
celery -A myproject beat -l info
```