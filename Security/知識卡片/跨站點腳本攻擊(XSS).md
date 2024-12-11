---
Date: 
tags:
  - 資安
---
## Proctection

為了防止存儲型 XSS 攻擊，請確保來自數據存儲的任何動態內容都不能用於在頁面上註入 JavaScript

- 轉義動態內容  
    網頁由 HTML 組成，通常在模板文件中描述，在呈現頁面時動態內容交織在一起。存儲型 XSS 攻擊利用了對來自後端數據存儲的動態內容的不當處理。攻擊者通過插入一些 JavaScript 代碼來濫用可編輯字段，當其他用戶訪問該頁面時，該代碼會在瀏覽器中進行評估。

除非您的站點是一個內容管理系統，否則您很少希望您的用戶編寫原始 HTML。相反，您應該轉義 來自數據存儲的所有動態內容，這樣瀏覽器就知道它被視為 HTML 標記的內容，而不是原始 HTML。  
以這種方式轉義可編輯內容意味著它永遠不會被瀏覽器視為可執行代碼。這為大多數 XSS 攻擊關閉了大門。

- 白名單值  
    如果特定的動態數據項只能採用少量有效值，則最佳做法是限制數據存儲中的值，並讓您的呈現邏輯僅允許已知的良好值。例如，不要讓用戶輸入他們居住的國家，而是讓他們從下拉列表中進行選擇。
    
- 實施內容安全政策  
    現代瀏覽器支持內容安全策略 ，允許網頁作者控制從何處加載和執行 JavaScript（和其他資源）。XSS 攻擊依賴於攻擊者能夠在用戶的網頁上運行惡意腳本——通過在頁面script標記內的某處注入內聯標記html，或者通過誘使瀏覽器從惡意第三方域加載 JavaScript。  
    通過在響應標頭中設置內容安全策略，您可以告訴瀏覽器永遠不要執行內聯 JavaScript，並鎖定哪些域可以為頁面託管 JavaScript：
    

```
<meta http-equiv="Content-Security-Policy" content="script-src 'self' https://apis.google.com">
```

>[!info]
>**這種方法將非常有效地保護您的用戶**! 但是，可能需要相當多的紀律才能讓您的網站為這樣的標題做好準備。內聯腳本標籤在現代網絡開發中被認為是不好的做法——混合內容和代碼使網絡應用程序難以維護——但在舊的遺留網站中很常見。

要逐步擺脫內聯腳本，請考慮使用 [CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) 違規報告。通過在策略標頭中添加report-uri指令，瀏覽器將通知您任何違反策略的行為，而不是阻止內聯 JavaScript 執行：

僅內容安全策略報告：腳本源“自我”；報告 uri [http://example.com/csr-reports](http://example.com/csr-reports)  
這將使您放心，在您完全禁止它們之前，沒有揮之不去的內聯腳本。

- 清理 HTML  
    一些站點有存儲和呈現原始 HTML 的合法需要。如果您的站點存儲和呈現豐富的內容，您需要使用 HTML 清理庫來確保惡意用戶無法在他們的 HTML 提交中註入腳本。
    
<details> <summary>顯示/隱藏內容</summary> **內容** </details>

## 代碼示例

Django 中的模板默認轉義 HTML，所以任何看起來像下面這樣的東西通常都是安全的：


### Python

```
**{{ contents }}**
```

您可以使用 | safe 過濾器覆蓋轉義。通常有充分的理由這樣做，但您需要對使用此命令的任何內容進行代碼審查：

```
**{{ contents | safe }}**
```

{% autoescape %} 請注意，也可以使用標記打開或關閉 HTML 轉義 。

### Node

Mustache.js  
雙鬍子中的標籤會自動轉義 HTML：

```
{{ contents }}
```

三鬍子中的標籤不會轉義 HTML，應謹慎使用：

```
{{{ contents }}}
```

Dust.js  
關鍵標籤自動轉義 HTML：

```
{ contents }
```

但是，可以使用|s操作員禁用轉義，因此請謹慎使用

```
{ contents | s }
```

Nunjucks  
如果環境中開啟了自動轉義，Nunjucks 會自動轉義標籤以安全輸出：

```
{{ contents }}
```

標有安全過濾器的內容不會被轉義——請謹慎使用此功能：

```
{{ contents | safe }}
```

可以為模板禁用自動轉義，在這種情況下標籤需要手動轉義：

```
{{ contents | escape }}
```

### PHP

默認情況下，該echo命令不會轉義 HTML，這意味著任何像下面這樣直接從 HTTP 請求中提取數據的代碼都容易受到 XSS 攻擊：

```
<?php
  echo $_POST["comment"];
?>
```

請務必使用strip_tags函數或 htmlspecialchars函數來安全地轉義參數：

```
<?php
  echo strip_tags($_POST["comment"]);
?>
```

### Reat

在 React 中，任何寫在大括號中的動態內容都會自動轉義，因此以下內容是安全的：

```
render() {
  return <div>{dynamicContent}</div>
}
```

React 允許您通過將內容綁定到屬性來寫出原始 HTML，該 dangerouslySetInnerHTML屬性的命名是為了提醒您安全風險！注意任何類似於以下內容的代碼：

```
render() {
  return <div dangerouslySetInnerHTML={ __html: dynamicContent } />
}
```

## 其他注意

僅限 HTTP 的 Cookie  
我們的示例 hack展示了會話劫持攻擊如何使用惡意 JavaScript 竊取包含用戶會話 ID 的 cookie。很少有充分的理由在客戶端 JavaScript 中讀取或操作 cookie，因此請考慮將 cookie 標記為 HTTP-only，這意味著瀏覽器將接收、存儲和發送 cookie，但不能由 JavaScript 修改或讀取。