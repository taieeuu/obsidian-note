---
Date: 2024-06-04
tags:
  - docker
  - devops
---
## 為什麼不建議在生產環境中使用docker-Compose
- 多機器如何管理？
- 如果跨機器做scale橫向擴充？
- 容器失敗退出時如何新建容器確保服務正常運作？
- 如何確保零宕機時間？
- 如何管理密碼，Key等敏感資料？
## 容器編排swarm
![[Pasted image 20240604205814.png]]

Swarm的基本架構
![[Pasted image 20240604205833.png]]