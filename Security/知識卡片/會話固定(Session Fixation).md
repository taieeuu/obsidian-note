---
Date: 2024-02-07
tags:
  - 資安
---
## Protection

- 不要在 GET/POST 變量中傳遞會話 ID  
    在查詢字符串或 POST 請求正文中傳遞會話 ID是有問題的。它不僅可以製作惡意 URL，而且可以通過以下方式洩露會話 ID：
    
    - 如果用戶點擊出站鏈接（Referer標題將描述用戶瀏覽的位置）。
    - 在瀏覽器歷史記錄和書籤中。
    - 在您的 Web 服務器和任何代理服務器上的日誌中。  
        會話 ID 最好在HTTP cookie中傳遞。有關如何執行此操作的示例，請參見下面的代 碼示例。
- 在身份驗證時重新生成會話 ID  
    會話固定攻擊可以通過簡單地在用戶登錄時重新生成會話 ID 來擊敗。
    
- 只接受服務器生成的會話 ID  
    確保您的 Web 服務器只接受服務器生成的會話 ID 是一種很好的做法。（不過，就其本身而言，這並不能解決會話固定漏洞。黑客可以輕鬆獲得服務器生成的新 ID 並將其通過精心設計的 URL 傳遞給受害者。）
    
- 超時並替換舊會話 ID  
    定期更換會話 ID 作為第二層防禦，以防它們被洩露。
    
- 實現強大的註銷功能  
    您網站上的註銷功能應將會話 ID 標記為已過時。（你確實有註銷功能，對吧？）
    
- 從可疑推薦人訪問時需要新會話  
    如果用戶從單獨的網站（例如網絡郵件）訪問您的網站，請考慮強制他們重新登錄。
## 代碼示例
### Python
Django 默認使用 cookie 跟踪會話 ID，但您必須在設置文件中啟用會話 。調用 cycle_key() 以在登錄後重置會話 ID。
### Ruby
基於 cookie 的會話存儲是 Rails 的默認設置，它為您提供了防止會話固定的強大保護。為了使事情完全安全，您應該通過調用登錄過程來重置會話 ID reset_session （通常這是通過SessionsController#create 操作實現的。）
### Java
Servlets/Java 服務器頁面  
在 Java servlet 容器中，您可以通過修改 web.xml. 最佳做法是使用 cookie 跟踪會話 ID。如果可能，您還應該強制您的服務器僅通過 HTTPS 跟踪會話 ID，並在一段時間不活動後超時會話。

要在身份驗證後重新生成會話 ID，請調用 HttpSession.invalidate()，然後使用 創建一個新會話 HttpServletRequest.getSession(true)。

```
<session-config>
  <tracking-mode>COOKIE</tracking-mode>
  <session-timeout>15</session-timeout>
  <cookie-config>
    <secure>true</secure>
  </cookie-config>
</session-config>
```
### C＃
[ASP.NET](http://asp.net/)  
默認情況下，[ASP.NET](http://asp.net/) 使用 cookie 跟踪會話 ID。強制服務器重新生成會話 ID 在 .NET 中並不簡單 - 考慮使用NWebSec 庫來確保會話 ID 的安全處理。
### Node
快遞.js  
Express 在 cookie 中跟踪會話 ID。有趣的是，您可以提供自己的會話 ID 生成算法（推薦的方法是使用 UUID）。您可以使用以下命令強制生成新的會話 ID：

```
req.session.regenerate(function(err) {
  // New session here
})
```
### PHP
在 PHP 中使用 cookie 跟踪會話。使用 強制生成新的會話 ID session_regenerate_id(true)。