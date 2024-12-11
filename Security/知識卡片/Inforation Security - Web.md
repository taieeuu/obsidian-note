---
Date: 2024-02-07
tags:
  - 資安
---

WAF(Web Application Firewall):  
Web 應用防火牆-通過執行一系列對http/https的安全策略，並專門為網站提供保護一款產品

網站保護  
階層一 - CDN內容傳遞網路、DDos Mitigation阻斷服務攻擊緩解  
階層二 - Firewall防火牆、IDs入侵偵測系統、IPs入侵防禦系統  
階層三 - M-WAF管理型網站應用程式防火牆  
階層四 - HackerScan網頁掛馬入侵偵測  
階層五 - SSL數位憑證

IDs (Intrusion Detection Sys)  
入侵檢測系統 - 網路案全裝置，可以監控網路傳輸或系統，是否有可疑活動。

IPS (Intrusion Prevention Sys)  
入侵預防系統 - 監視網路或網路裝置得網路傳輸行為，能夠及時中斷、調整或隔離資料

```
HIPS (host IPS) 主機入侵保護
	用於保護伺服器主機系統不受到入侵與破壞
NIPS (network IPS) 網路入侵保護
	用於保護網路架構體系，避免惡意流量阻塞網路
```