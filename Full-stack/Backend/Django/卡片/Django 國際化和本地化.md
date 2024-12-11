---
Date: 2024-08-21
---
Django 提供了強大的國際化（i18n）和本地化（l10n）功能，使開發者能夠輕鬆地構建支持多語言和多地域的應用程式。這些功能允許你根據用戶的語言偏好和所在區域自動調整應用程式的文本、日期格式、數字格式等。
### 國際化（Internationalization, i18n）
為應用程式增加多語言支持，使其可以方便地轉換為其他語言。Django 提供了一組工具來幫助開發者翻譯應用程式中的文本。
#### 設置語言
首先，你需要在 `settings.py` 中設置你的應用支持的語言和默認語言。

```python
# myproject/settings.py

LANGUAGE_CODE = 'zh-hant'  # 默認語言設置為繁體中文

# 定義支持的語言
LANGUAGES = [
    ('en', 'English'),
    ('zh-hant', '繁體中文'),
    # 其他語言
]

# 告訴 Django 你使用的翻譯文件的位置
LOCALE_PATHS = [
    os.path.join(BASE_DIR, 'locale'),
]
```
#### 標記可翻譯字符串
在 Django 中，你可以使用以下標籤來標記需要翻譯的字符串：

- **`gettext` 和 `gettext_lazy`**：標記需要翻譯的字符串，通常在 Python 代碼中使用。
- **`{% trans %}`**：在 Django 模板中標記需要翻譯的字符串。
- **`{% blocktrans %}`**：用於翻譯包含變量的模板片段。

```python
from django.utils.translation import gettext as _

def my_view(request):
    output = _("Welcome to my site.")
    return HttpResponse(output)
```

在模板中：

```django html
<p>{% trans "Welcome to my site." %}</p>
```

如果有變數，可以使用 `blocktrans`：

```django html
{% blocktrans %}Hello {{ user.username }}!{% endblocktrans %}
```
#### 提取翻譯字符串
標記完所有需要翻譯的字符串後，使用 `django-admin` 命令生成翻譯文件：

```bash
django-admin makemessages -l zh_Hant
```

這將在 `locale` 目錄下生成一個 `.po` 文件，你可以在其中為標記的字符串提供翻譯。
#### 編譯翻譯文件
翻譯完成後，你需要編譯 `.po` 文件生成 `.mo` 文件：

```bash
django-admin compilemessages
```

這會將 `.po` 文件編譯成 Django 能夠使用的 `.mo` 文件。
#### 使用翻譯
Django 會根據用戶的語言首選項來自動選擇合適的語言。如果需要手動切換語言，你可以使用 `translation.activate()` 函數。

```python
from django.utils import translation

def my_view(request):
    user_language = 'zh-hant'
    translation.activate(user_language)
    response = HttpResponse(...)
    response.set_cookie(settings.LANGUAGE_COOKIE_NAME, user_language)
    return response
```
### ### 本地化（Localization, l10n）
本地化指的是將數字、日期、時間等根據用戶的地域習慣進行格式化顯示。Django 提供了多種工具來幫助開發者實現本地化。
#### 啟用本地化
在 `settings.py` 中設置本地化選項：

```python
# myproject/settings.py

USE_I18N = True  # 啟用國際化
USE_L10N = True  # 啟用本地化
USE_TZ = True    # 啟用時區支持
```
#### 日期和時間的本地化
Django 會根據用戶的地區自動格式化日期和時間。你可以使用模板標籤 `{{ date_obj }}` 或 `date` 標籤來顯示本地化的日期和時間。

```django html
<p>Today is {{ my_date|date:"DATE_FORMAT" }}.</p>
```
#### 數字和貨幣的本地化
Django 也支持數字和貨幣的本地化顯示。你可以使用 `localize` 標籤來強制本地化數字格式：

```python
{% load l10n %}

{% localize on %}
    {{ value }}
{% endlocalize %}
```
#### 翻譯 URL
如果你的應用支持多語言，你可能需要為不同語言提供不同的 URL 結構。Django 提供了 `i18n_patterns` 來幫助實現這一點：

```python
from django.conf.urls.i18n import i18n_patterns
from django.urls import path

urlpatterns = i18n_patterns(
    path('about/', my_view, name='about'),
    # 其他 URL 模式
)
```

這樣，`/en/about/` 和 `/zh-hant/about/` 會指向相同的視圖，但顯示不同語言的內容。