---
Date: 2024-02-07
tags:
  - 資安
---
## Protection
- 禁用內聯 DTD 的解析  
    內嵌 DTD 是一個很少使用的特性。然而，XML 炸彈仍然是一個常見的漏洞，因為許多 XML 解析庫默認情況下不會禁用此功能。如果您使用 XML 解析，請確保您的解析器配置禁用此功能。請參閱下面的代碼示例，或查閱您的 API 文檔以了解操作方法。
    
- 考慮使 XML 解析異步  
    解析大型 XML 文件會佔用大量時間和內存。如果您的體系結構還沒有這樣做，請考慮使大型 XML 文件的解析成為異步的。上傳 XML 文件時，將它們移到一個隊列中，並讓一個單獨的進程將它們從隊列中彈出並處理解析任務。
    

這種方法將提高系統的可擴展性和穩定性，因為繁重的解析工作不會使您的 Web 服務器脫機。（這裡 AJAX 請求是個例外——它們需要由 Web 服務器處理，因為它們是 HTTP 請求-響應週期的一部分。）

- 限制每個客戶端的上傳  
    如果您接受來自已識別帳戶的 XML 上傳，最好限制每個帳戶同時解析作業的數量。如果下游系統開始使您的系統過載，這將保護您免受意外的拒絕服務攻擊。