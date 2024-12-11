---
Date: 2024-02-07
tags:
  - 資安
---
## Protection

**購買證書，安裝它，然後配置您的 Web 服務器以使用它。**

就這麼簡單。Web 服務器通常能夠通過 HTTP（在端口 80 上）和 HTTPS（在端口 443 上）提供相同的內容。任何重要的網站都應該使用 HTTPS。Facebook 和Twitter 默認使用 HTTPS，這是一個很好的例子。

**但是請確保您知道如何強制您的 Web 服務器提升到安全連接，並在用戶進行身份驗證或建立會話時這樣做。**執行此操作的一種常見方法是確保將 cookie 設置為 secure——這樣，會話只能通過 HTTPS 建立。

## 代碼示例

### 反向代理

在 Web 服務器和外部世界之間放置Apache或Nginx是很常見的。如果您有此設置，則很容易將 HTTP 請求重定向為使用 HTTPS。在 Apache 中，重寫規則如下所示：

```


RewriteEngine On
RewriteCond %{SERVER_PORT} !^443$
RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [QSA,NC,R,L]
```

nginx

```




server {
  listen 80;
  rewrite ^(.*) https://$host$1 permanent;
}

```

### Python

要強制 Django 使用 HTTPS，最簡單的方法是安裝 django-sslify 模塊。為確保 cookie 僅通過安全連接傳輸，請在您的配置中包含以下選項：  
SESSION_COOKIE_SECURE = True

### Java

In your web.xml, set the following option to ensure cookies are only transferred over HTTPS:

```
<session-config>
  <cookie-config>
    <secure>true</secure>
  </cookie-config>
</session-config>
```
Tomcat  
See here for instructions on how to configure SSL in Tomcat.

