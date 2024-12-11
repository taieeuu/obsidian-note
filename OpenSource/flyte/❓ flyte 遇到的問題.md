## Question
### issue 4354
1.  我可以一開始使用auth = "" 進行初始化，然後如果收到401後，再使用 pkce 進行驗證嗎
### 知識點（釐清）
1. flytekit AuthType 與 flyte server 之間的驗證邏輯，為什麼設置不同的驗證會通過
   - 需要 Public Demain 才會啟用驗證
   