---
Date: 2024-02-07
tags:
  - 資安
---
## Proection

- 禁止異地重定向  
    您可以通過**檢查傳遞給重定向函數的 URL 來防止重定向到其他域**。確保所有重定向 URL 都是相對路徑——即它們以單個/字符開頭。（請注意，以 開頭的 URL //將被瀏覽器解釋為與協議無關的絕對 URL——因此它們也應該被拒絕。）  
    如果您確實需要執行外部重定向，請考慮限制您允許重定向的個別站點。
    
- 進行重定向時檢查引薦來源網址  
    重定向到**查詢參數中傳遞的 URL 應該只由您網站上的頁面觸發**。應高度懷疑任何其他觸發重定向的網站。作為第二層防禦，Referer無論何時執行重定向，都要檢查 HTTP 請求中的 是否與您的域匹配。
## 代碼示例
### Python
```



import re

def is_relative(url):https://www.hacksplaining.com/prevention/open-redirects#java70785346-30e3-43a3-9d18-6493ce3d0749-examples
  return re.match(r"^\/[^\/\\]", url)
```
### Java
- 危險

````
protected void doGet(HttpServletRequest request, HttpServletResponse response) {
  String url = request.getParameter("url");
  if (url != null) {
    response.sendRedirect(url);
  }
}
- 安全
``` java=
    protected void doGet(HttpServletRequest request, HttpServletResponse response) {
  String url = request.getParameter("url");
  if (url != null && isRelative(url)) {
    response.sendRedirect(url);
  }
}

// Allow anything starting with "/", except paths starting
// "//" and "/\".
private boolean isRelative(String url) {
  return url.matches("/[^/\\]?.*");
}

````
### .NET
```
private bool IsRelativePath(string url)
{
   if (string.IsNullOrEmpty(url))
   {
      return false;
   }
   else
   {
      if (url.Length == 1) return true;

      // Allow anything starting with "/", except paths starting
      // "//" and "/\". Allow the "~/" syntax too.
      return (url[0] == '/' && (url[1] != '/' && url[1] != '\\')) ||
             (url[0] == '~' && url[1] == '/');
   }
}
```

>[!Note]
>.NET 允許~/相對於您站點域的 URL 語法 - 您可以選擇是否在重定向中支持此語法。
### Node
```
function isRelative(url) {
  return url && url.match(/^\/[^\/\\]/);
}
```