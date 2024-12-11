---
Date: 2024-05-18
---
原本是使用conda進行環境的分離來進行開發，但今天看到主管在開發的過程就使用了docker，在未來要上線的時候，也可以就使用這個dockerfile或是docker-compose來進行佈署，不會在後面發生一些錯誤，然後詢問了一下主管為甚麼這樣做等等之類的問題後覺得還滿有趣的，也覺得對於自己的開發有幫助就研究了一下。
## Step 0
首先我們要在VSCode中安裝`Docker`、`dev containers`或是直接安裝`remote developement`
## Step 1
在VSCode中按下`ctrl + shift + p`後輸入**Add Dev Container** ，然後選取**開發容器: 新增開發容器設定檔**，然後就依序選擇你要的。
## Step 2
這時會生成，1. 名為 .devcontainer 的新資料夾已新增至專案。2. 展開該資料夾，並注意其包含 devcontainer.json 檔案。這個檔案配置你要的image或是其他的設定。
## Step 3
一樣按下`ctrl + shift + p`，輸入**在容器中重新開啟**，選擇**開發容器:在容器重新開啟**
容器將會開始建立。 初始組建可能需要幾分鐘的時間，因為必須將新映像拉下並在您的電腦上建置。 第一次建立容器後，之後的建置速度將會大幅提升。

之後我們就可以看到左下角有個Dev Container，且terminal會呈現vscode的樣式。
![[VSCode 連線 Docker 進行開發 1.png]]
![[VSCode 連線 Docker 進行開發 2.png]]

參考: https://learn.microsoft.com/zh-tw/training/modules/use-docker-container-dev-env-vs-code/5-customize-settings