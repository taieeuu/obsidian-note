---
Date: 2024-08-21
---
對於 Web 動態網站而言，其基本權衡是動態的。每次用戶請求頁面時，Web 服務器都會進行各種計算，包括從數據庫查詢數據、將模板渲染成實際頁面、處理業務邏輯。相較傳統網頁而言，成本要高很多。

對於大多數 Web 應用程序，此開銷並不大，因為大多數 Web 應用程序只是流量一般中小型網站。但是，對於中到高流量站點，則必須盡可能減少開銷，這就是 Web 緩存由來。
### 為什麼需要緩存？
緩存可以大幅減少伺服器的負擔和資料庫查詢的次數，從而提高整個應用程式的效能。具體來說，緩存可以幫助：

- **減少資料庫查詢**：將經常使用的資料緩存在記憶體中，避免每次請求都查詢資料庫。
- **加快頁面加載速度**：對於不經常變動的頁面或片段，緩存能避免每次都重新渲染模板。
- **減少伺服器負載**：通過重用緩存的內容，減少伺服器的計算量和資源消耗。
### 緩存類型與配置
#### 緩存類型
##### 本地記憶體緩存（Local-memory caching）
這是一種將緩存存儲在本地記憶體中的緩存方式。這種緩存只適用於單個進程，因此不適合多進程或分佈式環境。它非常快，適合用於開發環境或簡單的生產應用。

```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
        'LOCATION': 'unique-snowflake',
    }
}
```
##### 文件系統緩存（File-based caching）
Django 內置默認的緩存保存方式，將緩存存放在計算機的內存中，只適用於項目開發測試。

```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': '/var/tmp/django_cache',
    }
}
```
##### Memcached 緩存
一個高性能的分布是內存對象緩存系統，主要用於動態網站，以減輕數據庫負載，他通過在內存中緩存數據和對像來減少讀取數據庫的次數，從而提高網站的響應速度。要使用 Memcached ，需要安裝 Memcached 系統服務器，Django 通過 python-memcached 或 pylibmc 模塊調用 Memcached 系統伺服器，時線緩存讀寫操作。Memcached 適合超大型網站使用。

```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        #  'BACKEND': 'django.core.cache.backends.memcached.PyLibMCCache'
        'LOCATION': '127.0.0.1:11211',
    }
}
```
##### Redis 緩存
Redis 是一種功能強大的內存數據庫，支持更多的數據結構和持久化選項。它適合於需要高性能、持久化和分佈式支持的應用。

```python
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/1',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
        }
    }
}
```
##### 資料庫緩存（Database caching）
緩存訊息儲存在網站數據庫的緩存表中，緩存表可以在項目的配置文件中配置。數據庫緩存適合大中型網站使用。

需要創建一個用於存儲緩存數據的資料庫表：

```sh
python manage.py createcachetable
```

```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.db.DatabaseCache',
        'LOCATION': 'my_cache_table',
    }
}
```

Django 支援多種緩存後端，例如內存緩存、本地快取檔案系統、Memcached 和 Redis。首先，在 `settings.py` 中配置緩存。

```python
# myproject/settings.py
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
        'LOCATION': 'unique-snowflake',
    }
}
```

這裡配置了一個本地內存緩存，適合用於開發或小型應用。如果你的應用程式規模較大，建議使用 Redis 或 Memcached 來進行緩存。
##### 使用 Redis 作為緩存後端
首先，確保已安裝 Redis 和 Django-Redis：

```sh
pip install redis django-redis
```

在 `settings.py` 中進行如下配置：

```python
# myproject/settings.py

CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/1',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
        }
    }
}
```
### 緩存的使用方式
Django 提供了多種使用緩存的方式，主要包括：

- **整個網站的緩存**：對整個網站的回應進行緩存。
- **視圖級別的緩存**：對單個視圖的回應進行緩存。
- **模板片段緩存**：對模板的一部分進行緩存。
- **手動緩存**：開發者可以手動緩存特定資料。
- **路由緩存**: 其作用與是圖緩存相同，但兩者是有區別的。例如兩個路由只像同一個視圖函數，在分別訪問這兩個路由地址時，路由緩存會先判斷路由地址是否已生成緩存，再決定是否執行視圖函數。
##### 網站緩存
要對整個網站進行緩存，可以在 `MIDDLEWARE` 中添加 `UpdateCacheMiddleware` 和 `FetchFromCacheMiddleware`：

```python
# myproject/settings.py

MIDDLEWARE = [
	# 配置全站緩存
    'django.middleware.cache.UpdateCacheMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.cache.FetchFromCacheMiddleware',
    # 其他中介軟體
]
# 屬性default來自緩存配置CACHES的default
CACHE_MIDDLEWARE_ALIAS = 'default' 
# 設置緩存的生命周期
CACHE_MIDDLEWARE_SECONDS = 600  # 緩存時間（秒）
# 設置緩存表字段的cache_key的值
# 用於同一個Django項目多個站點之間共享緩存
CACHE_MIDDLEWARE_KEY_PREFIX = ''
```

這種方法適合對於變動不大的網站，能有效減少對伺服器的負載。
##### 視圖緩存
如果只想對某些視圖進行緩存，可以使用 `cache_page` 裝飾器：

```python
from django.views.decorators.cache import cache_page

@cache_page(60 * 15)  # 緩存 15 分鐘
def my_view(request):
    # 處理視圖邏輯
    return HttpResponse('Hello, World!')
```
##### 模板片段緩存
對於需要緩存的模板片段，Django 提供了 `cache` 標籤來緩存模板的某一部分內容：

```django html
{% load cache %}

{% cache 500 sidebar %}
    <div id="sidebar">
        <!-- 側邊欄內容 -->
    </div>
{% endcache %}
```

這樣只會緩存側邊欄，而非整個頁面，適合那些需要頻繁更新頁面但部分內容穩定不變的情況。
##### 手動緩存
手動緩存是指開發者根據需要，通過 Django 提供的緩存 API 手動將數據保存到緩存中並隨後讀取：

```python
from django.core.cache import cache

# 將數據保存到緩存中
cache.set('my_key', 'my_value', timeout=60*15)  # 緩存 15 分鐘

# 從緩存中讀取數據
value = cache.get('my_key')

# 如果緩存中不存在 key，可以設置一個默認值
value = cache.get('my_key', 'default_value')
```

這種方法適合需要對特定數據進行精細控制的場景。
### 路由緩存
路由緩存是再路由文件 urls.py 中生成和保存，路由緩存也是使用緩存函數 cache_page 實現的。

```python
from django.urls import path
from . import views
from django.views.decorators.cache import cache_page

urlpatterns = [
	# 在網站首頁設置路由緩存
	path('', cache_page(timeout=10, cache='MyDjango',
		key_prefix='MyURL')(views.index), name='index')
]
```
### 緩存的失效策略
緩存的失效策略決定了緩存中的數據何時過期並被移除。Django 提供了多種方法來管理緩存的失效：

- **超時設置**：每個緩存項目可以設置一個 timeout，當超過該時間時緩存自動失效。
- **手動失效**：開發者可以通過 `cache.delete(key)` 或 `cache.clear()` 手動刪除某個鍵或清空所有緩存。
### 高級緩存技術
除了基本的緩存策略，Django 還支持更多高級緩存技術，例如：

- **鍵的版本管理**：使用 `version` 參數來管理緩存的版本，可以在更新數據後強制使舊版本的緩存失效。
- **基於鍵前綴的緩存清理**：通過鍵前綴來組織緩存，便於對某一組相關數據進行統一的緩存清理。