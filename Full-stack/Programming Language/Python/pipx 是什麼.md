---
Date: 2024-10-10
---
就我理解而言，他是一個專門用來安裝和管理 **Python 命令行應用程式** 的工具，他可以為每個命令行應用程式提供一個虛擬環境，且各自的依賴是相互隔離的，也就是不同的Python版本，類似於 Ubuntu 的 `apt`，可以管理命令應用程式的安裝。但由於這些應用程式的名稱會被添加到全域的執行路徑中，**同名的應用程式不能同時安裝不同的 Python 版本**，除非你改變它們的名稱。

什麼是**命令行應用程式** : 他其實是一個 Python script ，可以通過命令行接受指令並執行相關操作，像 Git、curl 等等這樣的工具。