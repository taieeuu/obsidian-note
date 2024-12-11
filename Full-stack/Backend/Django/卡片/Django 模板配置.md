---
Date: 2024-08-11
tags:
  - Django
  - Python
---
在 web 開發中，模板是一種特殊的 HTML 模板，他勘入了一些能夠讓 Django 是別的變量和指令，然後由 Django 的模板引擎解析這些變量和指令，從而生成完整 HTML 網頁內容返回給用戶。

模板是 Django 的 MTV 框架模式的 T 部分，配置模板路徑就是告訴 Django 在解析模板時，如何找到模板所在位置。如下。

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates',
		        BASE_DIR / 'index/templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

參數說明如下。
* `BACKEND` : 定義模板引擎，用於識別模板中的變量和指令。內置的模板引擎有`Django Templates` 和 `Jinja2` ，每個模板引擎都有自己的變量和指令語法。
* `DIRS` : 設置模板所在路徑。
* `APP_DIRS` : 是否在 APP 中查找模板文件。
* `OPTIONS` : 用於填充在 `RequestContext` 的上下文，一般情況不需要做任何修改。

一般情況下，根目錄下的 `templates` 文件夾用於存放共用的模板文件，可以被各個 APP 的模板文件調用，這個模式符合代碼重複使用原則。