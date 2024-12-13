---
Date: 2024-07-06
tags:
  - devops
  - git
---
### Git 分支與合併 (Git Branch and Git Merge)
1. **沒有人應該直接在 Master 分支上工作。**
    - 建議避免直接在主分支（Master）上進行開發工作，以避免不必要的錯誤或影響穩定版本。
2. **我們在自己的分支上工作，這些分支可以是功能分支或修復分支。**
    - 每個開發人員應該在自己專屬的分支上進行開發工作。這些分支可以是新的功能分支（feature branch）或是用來修復錯誤的修復分支（bugfix branch）。
3. **當你認為你的工作完成時，嘗試將你自己的分支合併到主分支。**
    - 當功能或修復完成並經過測試後，將自己的分支合併回主分支（Master）以更新主版本。

這些原則有助於保持代碼庫的穩定性和整潔，並且使多人協作開發更加順利。