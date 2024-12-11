---
Date: 2024-02-07
tags:
  - 資安
---
## Protection

- 禁用“服務器”HTTP 標頭和類似標頭  
    在您的 Web 服務器配置中，確保禁用任何顯示您正在運行的服務器技術、語言和版本的 HTTP 響應標頭。
    
- 使用乾淨的 URL  
    盡量避免在.php、.asp和.jsp等 URL 中使用明顯的文件後綴 –而是實施乾淨的 URL 。
    
- 確保 Cookie 參數是通用的  
    確保沒有在 cookie 中發回提供有關技術堆棧線索的任何內容。這包括說明性參數名稱，應盡可能通用。
    
- 禁用客戶端錯誤報告  
    大多數 Web 服務器堆棧允許在發生意外錯誤時打開詳細錯誤報告——這意味著堆棧跟踪和路由信息會打印在錯誤頁面的 HTML 中。確保在您的生產環境中禁用此功能。日誌文件和其他錯誤報告系統在您的測試環境中很有用，但在生產環境中，錯誤報告應僅限於服務器端。
    

**確保意外錯誤返回通用 HTTP 500 頁面**。 根據您的技術堆棧，這可能需要明確捕獲在處理 Web 請求時拋出的意外異常。

- 清理傳遞給客戶端的數據  
    確保頁面和 AJAX 響應僅返回所需的數據。如果可能，數據庫 ID 應該被混淆——如果您為用戶保留敏感數據，請確保它只在可以共享的上下文中發送到客戶端。
    
- 混淆 JavaScript  
    這將使您的頁面加載速度更快，並且還會使攻擊者更難探測客戶端漏洞。
    
- 清理模板文件  
    進行代碼審查並使用靜態分析工具來確保敏感數據不會出現在註釋中或傳遞給客戶端的死代碼中。
    

確保正確配置 Web 根目錄  
確保將公共目錄和配置目錄嚴格分開，並確保團隊中的每個人都知道其中的區別。
## 代碼示例
### Python

Django 自帶一個global_settings.py 文件。個別設置可以被環境特定的配置文件覆蓋。

通過設置配置會話 ID 參數SESSION_COOKIE_NAME：

```





############
# SESSIONS #
############

# Cookie name. This can be whatever you want.
SESSION_COOKIE_NAME = 'session'
```

錯誤報告的詳細程度由 DEBUG 配置中的標誌控制。true值在錯誤頁面上打印堆棧跟踪。false值將返回安全的通用錯誤頁面，並通過電子郵件向管理員報告錯誤。

### Ruby

配置Server標頭的方法因服務器而異，因此請查閱您的文檔。用於標識會話的參數可以在配置中定義：

```
Rails.application.config.session_store :cookie_store, key: 'id'
```

Rails 為本地、測試和生產環境打包了合理的默認值，因此只要您確保運行正確的配置，錯誤頁面就不應在生產環境中洩露系統信息。通過包含各種 gem 在非生產環境中實現更明確的調試是很常見的。確保這些 gem 僅包含在您:development的.:testGemfile

Rails Asset Pipeline 使得通過單個配置更改引入 JavaScript 混淆變得非常容易 。

### Java
Tomcat  
Server通過編輯配置文件來隱藏標頭conf/server.xml：

```
<Connector port="8080" server="Server" />
```

web.xml通過編輯配置自定義會話 ID 參數的名稱：

```




<session-config>
  <cookie-config>
    <name>session</name>
  </cookie-config>
</session-config>
```

將意外錯誤路由到您選擇的正在編輯web.xml文件的頁面：

```










<error-page>
  <error-code>500</error-code>
  <location>unexpected-error.jsp</location>
</error-page>

<error-page>
  <exception-type>java.lang.Exception</exception-type>
  <location>unexpected-error.jsp</location>
</error-page>
```

確保您的自定義錯誤頁面僅在安全的情況下披露詳細的錯誤信息——即在非生產環境中！

如果您使用的是默認 servlet，請確保它不允許目錄列表：

```














<servlet>
  <servlet-name>default</servlet-name>
  <servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
  <init-param>
    <param-name>debug</param-name>
    <param-value>0</param-value>
  </init-param>
  <init-param>
    <param-name>listings</param-name>
    <param-value>false</param-value>
  </init-param>
  <load-on-startup>1</load-on-startup>
</servlet>
```

### .Net
要從 重命名會話 ID 參數ASP.NET_SessionId，請修改您的 web.config：

```
<system.web>
  <sessionState cookieName="session" />
</system.web>
```

要在您的生產環境中禁用調試信息，請確保您web.config包含以下標誌：

```
<configuration>
  <compilation debug="false"/>
</configuration>
```

您還需要設置自己的自定義錯誤頁面，因為默認錯誤頁面提供了很多錯誤詳細信息：

```
<customErrors mode="On" defaultRedirect="ErrorPageName.htm" />
```
