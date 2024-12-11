---
Date: 2024-08-11
tags:
  - Django
  - Python
---
### 資源路由 - Static_URL
靜態資源指網站部會改變的文件，通常包含 CSS 文件、JS 文件、圖片、字體等。

在項目發開過程中，肯定需要使用 CSS 和 JS 文件。在 Django 中，這些靜態文件的存放位置主要由配置文件`settings.py`中的配置項進行設置。默認如下。

```python
STATIC_URL = '/static/'
```

默認情況下，Django 只能識別項目應用中 static 文件夾中的靜態資源。查找功能主要通過 APP 列表 `INSTALLED_APPS` 的 `staticfiles` 項來實現。在 `<app_name>` 中，創建 `static` 文件夾並在該文件夾中放置圖片 `dog.jpg`。

需要注意的是，Django 在調試模式下只能識別項目應用中 `static` 文件夾內的靜態資源，如果將該文件夾更改為其他名稱，或者將該文件夾放在項目根目錄下，Django 則無法識別。

也就是說，當靜態資源的路由 `STATIC_URL` 的值為 `/static/` ，瀏覽器訪問靜態資源的 URL 必須以 `/static/`為前綴，否則無法訪問。同時 Django 在調試模式下只能識別項目應用目錄下的 `static` 文件夾中的靜態資源。
### 資源集合 - staticfiles_dirs
由於 `static_url` 的特殊性，在開發過程中可能會造成不便，比如將靜態文件夾存放在項目根目錄以及定義多個靜態文件等。

這樣的話，我們可以將根目錄的 `static` 文件夾和 `index` 目錄下的自定義資料夾中的靜態文件等靜態文件夾路徑添加到 `STATICFILES_DIRS`中。

```python
STATICFILES_DIRS = [
	# 根目錄下的static
	BASE_DIR / 'static',
	# index為APP，靜態文件在Mystatic
	BASE_DIR / 'index/Mystatic'
]
```

若想將訪問靜態文件的路由做更改，則需要重新啟動 Django 項目。

```python
STATIC_URL = '/Allstatic/'
```

訪問靜態文件網址，會變為 `localhost:8000/Allstatic/<靜態文件>`。
### 資源佈署
除了 `staticfiles_dirs` 和 `static_url` ，靜態資源配置還有一個重要的屬性 `static_root` ，其作用是在服務器上佈署項目時，收集整個項目的靜態資源並存放在一個新的文件夾中。以實現服務器和項目之間的映射關係。配置如下。

```python
STATIC_ROOT = BASE_DIR / 'AllStatic'
```

當項目配置屬性 `Debug = True` 時，Django 會自動提供靜態文件代理服務，此時整個項目楚瑜開發階段，因此無需使用 `static_root`。但當 `Debug = False` 時，異位進入生產環境，Django 不再提供靜態文件代理服務，此時需要配置 `static_root`。
### 媒體資源 - MEDIA
`STATIC_URL` 是配置靜態文件的路由地址，如 `CSS`、`JS` 等文件以及常用圖片等。但對於一些經常變動的資源，如用戶上傳的頭像、歌曲文件等，通常存放在媒體資源文件夾中。

媒體資源與靜態資源是可以同時存在，兩者可以獨立運行，互不影響。但與靜態資源不同的是，媒體資源只有配置屬性`MEDIA_URL` 和 `MEDIA_ROOT`。其中 `MEDIA_URL` 用於設置媒體資源的路由地址，`MEDIA_ROOT` 用於獲取 media文件夾完整路徑訊息。

```python 
# 設置媒體路由地址訊息
MEDIA_URL = '/media/'
# 獲取 media 文件夾的完整路徑訊息
MEDIA_ROOT = BASE_DIR / 'media'
```

配置完後，還需要將 mdeia文件夾註冊到 Django 中，否則則無法訪問文件夾的訊息。為此，在 `<Django-project>` 中的 `urls.py` 文件，並為膜體文件夾 `media` 添加相應的路由地址。

![[Django 資源文件配置 1.png]]
