---
Date: 2024-08-18
---
- Session 儲存在服務器端，Cookie儲存在庫扈端，所以`Session` 安全性比 `Cookie`高。
- 當獲取某用戶的 `Session` 數據時，首先從用戶傳遞的 `Cookie` 裡獲得 `Sessionid`，然後根據 `Sessionid` 在網站服務器找到相應的 `Session`。
- `Session` 存放在服務器的內存中，`Session` 的根據部段增加會造成服務器的負擔不段加重，因此存放在 `Session` 中的數據不能過於龐大。

在創建 Django 項目時，Django 以默認啟用 `Session` 功能，每個用戶的 `Session` 通過 Django 的中間件來接收與處理。

當訪問網站時，所有 Http 請求都經過中間件處理，而中間件 `SessionMiddleware` 會判斷當前請求的用戶接收器，而程序的執行則由 `settings.py` 的配置屬性 `INSTALLED_APPS` 的 `django.contrib.sessions` 完成。
### 會話的基本概念
會話是一種用來在用戶與網站之間維持狀態的機制。在 HTTP 協議中，所有的請求都是無狀態的，即每個請求都是獨立的，伺服器無法記住之前的請求。會話機制解決了這個問題，允許伺服器在多個請求之間保存和共享數據。
### 會話 ID
每個用戶的會話都由一個唯一的會話 ID 標識，這個會話 ID 存儲在用戶的瀏覽器 Cookie 中，並在每次請求時發送到伺服器。伺服器根據會話 ID 檢索和管理該用戶的會話數據。
### 會話數據存儲
Django 的會話框架允許你選擇將會話數據存儲在多種後端，如資料庫、文件系統或分布式緩存（如 Memcached、Redis）。
### 配置會話
Django 提供了多種配置選項來控制會話的行為。在 `settings.py` 文件中，你可以設置會話的存儲後端、會話過期時間、Cookie 的安全屬性等。
### 會話存儲後端
Django 支援以下幾種會話存儲後端：

- **資料庫後端**：會話數據存儲在資料庫中（默認選項）。
- **文件後端**：會話數據存儲在伺服器的文件系統中。
- **緩存後端**：會話數據存儲在緩存中（如 Memcached、Redis）。
- **簽名 Cookie 後端**：將會話數據存儲在 Cookie 中，並使用簽名來保證數據的完整性。

#### 設置資料庫後端（默認）
在 `settings.py` 中，Django 默認使用資料庫後端來存儲會話數據：

```python
SESSION_ENGINE = 'django.contrib.sessions.backends.db'
```

這將會話數據存儲在資料庫中的 `django_session` 表中。
#### 設置文件後端
要使用文件後端存儲會話數據，可以在 `settings.py` 中進行如下配置：

```python
SESSION_ENGINE = 'django.contrib.sessions.backends.file'
SESSION_FILE_PATH = '/path/to/your/session/directory'
```

如果 `SESSION_FILE_PATH` 未指定，Django 會使用系統的臨時文件夾。
#### 設置緩存後端
使用緩存來存儲會話數據可以提高性能。可以將會話數據存儲在 Memcached 或 Redis 中：
```python
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
SESSION_CACHE_ALIAS = 'default'  # 指定緩存別名
```
#### 設置簽名 Cookie 後端

簽名 Cookie 後端將會話數據存儲在 Cookie 中，適合不需要存儲大量會話數據的應用：

```python
SESSION_ENGINE = 'django.contrib.sessions.backends.signed_cookies'
```
### 會話過期時間
你可以通過 `SESSION_COOKIE_AGE` 設置會話的過期時間（以秒為單位）：
```python
SESSION_COOKIE_AGE = 1209600  # 兩周（默認）
```

要設置會話在用戶關閉瀏覽器時過期，可以啟用 `SESSION_EXPIRE_AT_BROWSER_CLOSE`：

```python
SESSION_EXPIRE_AT_BROWSER_CLOSE = True
```
### 會話的安全屬性
可以通過以下配置來增強會話的安全性：

- **`SESSION_COOKIE_SECURE`**：僅在 HTTPS 連接下發送 Cookie。
- **`SESSION_COOKIE_HTTPONLY`**：禁止通過 JavaScript 訪問 Cookie，減少 XSS 攻擊風險。
- **`SESSION_COOKIE_SAMESITE`**：防止跨站請求偽造（CSRF）攻擊。

```python
SESSION_COOKIE_SECURE = True
SESSION_COOKIE_HTTPONLY = True
SESSION_COOKIE_SAMESITE = 'Lax'
```
### 使用會話
#### 設置會話數據
在視圖中，你可以使用 `request.session` 訪問會話數據。設置會話數據的語法與設置字典值相似：

```python
def set_session_data(request):
    request.session['username'] = 'john_doe'
    request.session['cart'] = {'item_id': 1, 'quantity': 3}
    return HttpResponse("會話數據已設置")
```
#### 獲取會話數據
可以通過鍵來獲取會話中的數據。如果鍵不存在，可以返回默認值：

```python
def get_session_data(request):
    username = request.session.get('username', '訪客')
    cart = request.session.get('cart', {})
    return HttpResponse(f"用戶名：{username}, 購物車：{cart}")
```
#### 刪除會話數據
要刪除會話中的特定數據，可以使用 `del` 語句：

```python
def delete_session_data(request):
    try:
        del request.session['username']
    except KeyError:
        pass
    return HttpResponse("會話數據已刪除")
```
####  清空整個會話
你可以使用 `flush` 方法來清空整個會話數據，這也會刪除會話的 Cookie 並重新生成會話 ID：

```python
def flush_session(request):
    request.session.flush()
    return HttpResponse("會話已清空")
```
### 4. 會話的應用場景
#### 用戶認證
會話最常見的應用場景是用戶認證。在用戶成功登錄後，伺服器會在會話中設置用戶的身份信息，這樣用戶在後續的請求中不需要重新驗證身份。
#### 購物車
電子商務網站通常使用會話來管理用戶的購物車。在用戶瀏覽網站時，他們選擇的商品會被存儲在會話中，直到他們完成購買或清空購物車。
#### 臨時數據存儲
會話還可以用來存儲一些臨時數據，如表單數據、用戶偏好設置等，這些數據不需要永久存儲在資料庫中。