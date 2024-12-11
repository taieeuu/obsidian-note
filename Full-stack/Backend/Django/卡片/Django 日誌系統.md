---
Date: 2024-08-23
---
Django 使用 Python 內置日置紀錄模塊執行系統日置紀錄，主要包括了 `Logger`、`Handler`、`Filter` 和 `Formatter` 這四個模塊。
### Logger 
Django 應用通常由多個 Python 模塊組成，例如 `views.py`、`models.py`、`forms.py` 等。每個模塊可以有自己的 `logger`，這樣就可以更細粒度地控制日誌記錄。

`Logger` 具有日誌級別，描述了 `Logger` 將處理的消息的嚴重性。如下 :

- `Debug` : 用於調試目的的底層系統訊息
- `Info` : 系統訊息。
- `Warning` : 已發生的警告問題。
- `ERROR` : 已發生的主要問題。
- `Critical` : 已發生的嚴重問題。
#### 如何在模塊中設置 Logger
在每個 Django 模塊中，你可以設置一個 `logger` 專門用來記錄該模塊中的日誌信息。通常做法是在模塊的頂部創建一個 `logger` 對象：

```python
import logging

logger = logging.getLogger(__name__)
```

這裡的 `__name__` 是模塊的名稱，這樣創建的 `logger` 就會使用模塊名稱作為識別符，這是 Django 日誌系統的一個標準做法。
##### 例如，在 views.py 中可以這樣設置：
```python
import logging

logger = logging.getLogger(__name__)

def my_view(request):
    logger.info('處理請求 %s', request.path)
    # 視圖的其他代碼
```

這個 `logger` 的名稱將是 `myapp.views`，其中 `myapp` 是應用的名稱。
#### 配置 Logger 模塊
Django 的 `LOGGING` 配置允許你為不同的 `logger` 模塊設置不同的處理器和日誌級別。下面是一個配置示例，展示了如何為特定的 `logger` 模塊設置單獨的配置：

```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '{levelname} {asctime} {module} {message}',
            'style': '{',
        },
        'simple': {
            'format': '{levelname} {message}',
            'style': '{',
        },
    },
    'handlers': {
        'file': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': 'debug.log',
            'formatter': 'verbose',
        },
        'console': {
            'level': 'DEBUG',
            'class': 'logging.StreamHandler',
            'formatter': 'simple',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file', 'console'],
            'level': 'DEBUG',
            'propagate': True,
        },
        'myapp.views': {
            'handlers': ['file'],
            'level': 'INFO',
            'propagate': False,
        },
        'myapp.models': {
            'handlers': ['file', 'console'],
            'level': 'WARNING',
            'propagate': True,
        },
    },
}
```

我們先針對 `loggers` 模塊進行解析。
##### loggers django
這個 `logger` 是 Django 預設的日誌記錄器，負責處理 Django 框架本身的日誌。它可以捕獲與 Django 內部運作相關的所有日誌消息，包括中間件、請求、模板處理等。

```python
'django': {
    'handlers': ['file', 'console'],
    'level': 'DEBUG',
    'propagate': True,
},
```

- **`handlers`**: 指定了 `file` 和 `console` 兩個處理器。這意味著日誌會被記錄到文件中（通常是 `debug.log` 或類似的文件），並且同時輸出到控制台。
- **`level`**: 設置為 `DEBUG`，表示所有 `DEBUG` 級別及以上的日誌消息（包括 `INFO`、`WARNING`、`ERROR` 和 `CRITICAL`）都會被記錄。`DEBUG` 是最低級別，通常用於開發階段捕獲詳細的診斷信息。
- **`propagate`**: 設置為 `True`，表示這個 `logger` 的日誌消息會傳播到父級 `logger`。如果沒有明確設置父級 `logger`，則傳播到根 `logger`。

系面的`loggers`，以此類推。
### Handler
`handler` 是日誌系統中負責處理日誌記錄的組件。它決定了日誌消息的去向，例如是寫入文件、輸出到控制台，還是發送到遠程伺服器。每個 `handler` 可以根據需要使用不同的 `formatter` 來格式化日誌消息，並且可以配置不同的日誌級別來控制哪些消息應該被處理。
#### FileHandler
`FileHandler` 將日誌消息寫入到指定的文件中。
###### 示例:
```python
'handlers': {
    'file': {
        'level': 'DEBUG',
        'class': 'logging.FileHandler',
        'filename': 'debug.log',
        'formatter': 'verbose',
    },
},
```

- **`filename`**: 指定日誌文件的路徑。
- **`formatter`**: 指定使用的格式化器。
- **`level`**: 指定處理的日誌級別。
#### StreamHandler
`StreamHandler` 將日誌消息輸出到指定的流中，通常是標準輸出（控制台）。
###### 示例:
```python
'handlers': {
    'console': {
        'level': 'DEBUG',
        'class': 'logging.StreamHandler',
        'formatter': 'simple',
    },
},
```

這個 `handler` 會將日誌消息輸出到控制台。
#### RotatingFileHandler
`RotatingFileHandler` 是 `FileHandler` 的一種變體，當日誌文件達到一定大小時，會自動切換到一個新的文件，並保留多個備份。
###### 示例:
```python
'handlers': {
    'rotating_file': {
        'level': 'DEBUG',
        'class': 'logging.handlers.RotatingFileHandler',
        'filename': 'app.log',
        'maxBytes': 1024*1024*5,  # 5 MB
        'backupCount': 5,
        'formatter': 'verbose',
    },
},
```

- **`maxBytes`**: 設定文件大小限制（以字節為單位）。
- **`backupCount`**: 保留的備份文件數量。
#### TimedRotatingFileHandler
`TimedRotatingFileHandler` 按時間間隔自動切換日誌文件，比如每天、每周或每月創建一個新的日誌文件。
###### 示例:
```python
'handlers': {
    'timed_rotating_file': {
        'level': 'DEBUG',
        'class': 'logging.handlers.TimedRotatingFileHandler',
        'filename': 'timed_app.log',
        'when': 'midnight',
        'backupCount': 7,
        'formatter': 'verbose',
    },
},
```

- **`when`**: 指定滾動時間，可以是 `S` (秒), `M` (分鐘), `H` (小時), `D` (天), `midnight` (午夜)。
- **`backupCount`**: 保留的備份文件數量。
#### SMTPHandler
`SMTPHandler` 用於將日誌消息通過電子郵件發送出去，適合在發生嚴重錯誤時通知開發者。
###### 示例:
```python
'handlers': {
    'mail_admins': {
        'level': 'ERROR',
        'class': 'django.utils.log.AdminEmailHandler',
        'formatter': 'verbose',
    },
},
```

**`django.utils.log.AdminEmailHandler`** 是 Django 專門為發送日誌到管理員郵箱設計的處理器，依賴於 Django 的 `ADMINS` 配置。
#### NullHandler
`NullHandler` 不會處理任何日誌消息，通常用於禁用某些日誌記錄器。
###### 示例：
```python
'handlers': {
    'null': {
        'level': 'DEBUG',
        'class': 'logging.NullHandler',
    },
},
```
#### WatchedFileHandler
`WatchedFileHandler` 用於記錄到日誌文件中，並在日誌文件被外部程序轉儲或替換時自動處理這一變更。這個處理器通常在 Unix-like 系統中使用。
###### 示例：
```python
'handlers': {
    'watched_file': {
        'level': 'INFO',
        'class': 'logging.handlers.WatchedFileHandler',
        'filename': '/var/log/django/app.log',
        'formatter': 'verbose',
    },
},
```
### Filter
`filter` 模塊是一個用於進一步控制日誌消息處理的組件。它允許你在日誌消息到達 `handler` 之前對其進行過濾，從而實現更細粒度的日誌控制。通過使用 `filter`，你可以根據日誌消息的內容、自定義條件或其他屬性來決定哪些消息應該被記錄，哪些應該被忽略。
#### 內置 Filter
Django 提供了一些內置的 `filter`，例如：

- **`require_debug_true`**: 只在 `DEBUG=True` 時記錄日誌消息。
- **`require_debug_false`**: 只在 `DEBUG=False` 時記錄日誌消息。
這些內置 `filter` 對於控制日誌輸出非常有用，特別是在區分開發和生產環境時。
###### 示例：
```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'filters': {
        'require_debug_true': {
            '()': 'django.utils.log.RequireDebugTrue',
        },
    },
    'handlers': {
        'console': {
            'level': 'DEBUG',
            'class': 'logging.StreamHandler',
            'filters': ['require_debug_true'],
        },
    },
    'loggers': {
        'django': {
            'handlers': ['console'],
            'level': 'DEBUG',
            'propagate': True,
        },
    },
}
```

在這個例子中，只有在 `DEBUG=True` 時，日誌消息才會輸出到控制台。
#### 如何定義和使用 Filter
你可以通過創建一個自定義類來定義 `filter`。這個類需要實現 `filter` 方法，並返回一個布爾值來決定是否記錄日誌消息。
###### 示例：定義一個只記錄特定關鍵字的日誌 `filter`：
```python
import logging

class KeywordFilter(logging.Filter):
    def __init__(self, keyword):
        self.keyword = keyword

    def filter(self, record):
        return self.keyword in record.getMessage()

# 然後在 LOGGING 配置中使用這個 Filter
```
##### 在 LOGGING 配置中使用 Filter
你可以在 `settings.py` 中的 `LOGGING` 配置裡設置 `filter`。`filter` 可以應用於 `logger` 或 `handler`。以下是一個示例配置：

```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'filters': {
        'special': {
            '()': 'path.to.your.KeywordFilter',
            'keyword': 'special',
        },
    },
    'formatters': {
        'verbose': {
            'format': '{levelname} {asctime} {module} {message}',
            'style': '{',
        },
    },
    'handlers': {
        'file': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': 'debug.log',
            'formatter': 'verbose',
            'filters': ['special'],
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file'],
            'level': 'DEBUG',
            'propagate': True,
        },
    },
}
```

在這個配置中：

- **`filters`**: 定義了一個名為 `special` 的 `filter`，它使用了自定義的 `KeywordFilter` 類，並傳遞了一個關鍵字 `special`。
- **`handlers`**: 在 `file` 處理器中應用了這個 `filter`。這意味著只有包含關鍵字 `special` 的日誌消息才會被記錄到 `debug.log` 文件中。