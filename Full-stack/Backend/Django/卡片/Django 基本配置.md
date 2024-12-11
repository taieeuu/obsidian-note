---
Date: 2024-08-11
tags:
  - Python
  - Django
---
一個簡單的 `Django` 項目必須具備基本配置 : 項目路徑、密鑰配置、調試模式、域名訪問權限、App列表。如下

```python
from pathlib import Path
# 項目路徑
BASE_DIR = Path(__file__).resolve().parent.parent
# 密鑰配置，用於加密
SECRET_KEY = '#+m+r2vq)0fy2r((-=g4(m3betkfhj7ax5hbwf34v^+fb(kb8g'
# 調試模式
DEBUG = True
# 域名訪問模式
ALLOWED_HOSTS = []
# APP列表
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # 添加项目应用index
    'index'
]
```

配置說明:
* `BASE_DIR` : 讀取當前項目在本地中的問見路徑。創建項目時自動生成，一般無需修改。
* `SECRET_KEY` : 這是一個隨機值，在項目創建時自動生成，一般情況下無需修改。主要用於重要的數據加密處理，如用戶密碼、[[CSRF (跨站請求偽造)]]、和 [[Session]] 等數據加密等。
	* 用戶密碼 : Django內置一套 Admin認證系統，該系統具有用戶認證和存儲用戶信息等功能。在創建用戶時，將用戶密碼通過密鑰進行加密處理，保證用戶的安全性。
	* CSRF機制 : 主要用表單提交，防止黑客竊取網站的用戶新襲來製造惡意請求。
	* Session : Session 的信息存放在 Cookie 中，以一串隨機的字符表示，用於標示當前訪問網站的用戶身分，紀錄用戶訊息。
* `Debug` : 為布林值，在開發階段為 True，以便在開發吊飾過程中自動檢測戴碼是否發生更改，根據檢測結果來判斷是否刷新重啟系統。項目部署更改為 False，否則會洩漏相關用戶信息。
* `ALLOWED_HOSTS` : 設置可訪問的域名，默認空列表。當 `Debug` 為 True 並且 `ALLOWED_HOSTS` 為空列表時，項目址允許以 `localhost` 或是 `127.0.0.1` 在遊覽器上訪問。當 `Debug` 為 False 時，`ALLOWED_HOSTS` 為必須填寫項，否則程序無法啟動，允許所有可使用`*`。
* `INSTALLED_APPS` : 告訴 Django 有哪些 APP 。在項目創建時已有 `admin`、`auth` 和 `sessions` 等配置信息。
	* `admin` : 內置的後台管理系統
	* `auth` : 內置的用戶認證系統
	* `contenttypes` : 紀錄項目中所有model源數據 (Django的ORM框架)
	* `session` : 會話功能，用於標示當前訪問網站的用戶身分，紀錄相關用戶信息。
	* `messages` : 消息提示功能
	* `staticfiles` : 查找靜態資源路徑

如果在項目中創建新的 `APP` ，就必須將該 `APP` 名稱添加到 `INSTALLED_APPS` 列表中。