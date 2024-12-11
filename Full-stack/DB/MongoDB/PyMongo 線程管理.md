---
Date: 2024-06-20
---
如果因為安全問題，想要限制 `pymongo` 的連接數量的話，`pymongo` 有提供 **連接池** 的功能，`pymongo` 默認情況下是線程安全的，它會自動管理連接池，因此在多線程應用中使用時通常不需要進行額外的配置。

ps. mongoengine 也是繼成 pymongo 的方式
## 連接池

```python
from pymongo import MongoClient # 設置連接池大小 
client = MongoClient('mongodb://localhost:27017/', maxPoolSize=50, minPoolSize=10) # 使用客戶端 
db = client['mydatabase'] 
collection = db['mycollection'] # 執行一些 MongoDB 操作 
collection.insert_one({'message': 'Hello, MongoDB!'})
```
>[!info]
>連接池大小對性能的影響
>- **maxPoolSize 太小**: 當高併發請求來臨時，連接池中的連接數量不夠用，可能會導致請求等待甚至超時，從而影響應用程序的性能。
>- **maxPoolSize 太大**: 連接池中的連接數量過多，可能會浪費系統資源，特別是在連接數超過 MongoDB 服務器的處理能力時，反而會降低性能。

#### 調整連接池大小的最佳實踐

- 根據應用程序的具體需求和負載情況調整 `maxPoolSize` 和 `minPoolSize` 的值。
- 測試和監控應用程序的性能，根據結果進行調整。
- 如果不確定應該設置多大的連接池，可以從默認值開始，逐步調整並觀察應用程序的響應時間和資源使用情況。

當你使用 thread 創建多的連線時，假設你有監控 thread 你會發現它只會有一個 pymongo_server_monitor_thread ，但底層有多個連接由連接池管理，這些連接可以被多個 Thread 共享和使用。
