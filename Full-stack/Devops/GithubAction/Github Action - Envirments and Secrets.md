---
Date: 2024-06-09
---
>[!info]
>參考[官方文檔](https://docs.github.com/en/actions/learn-github-actions/variables#default-environment-variables)
## GitHub Action Default Environment Variables
```yml
name: CI
on: workflow_dispatch

jobs:
  build_on_linux:
    runs-on: ubuntu-latest
    steps:
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: check default env
        run: env
      - name: print env
        run: echo $GITHUB_REPOSITORY
      - name: test with python
        run: python env.py
```
>[!info]
>- **checkout code**：使用 `actions/checkout@v3` 動作檢出代碼庫。
>- **check default env**：運行 `env` 命令以顯示默認環境變量。
>- **print env**：使用 `echo $GITHUB_REPOSITORY` 命令打印 `GITHUB_REPOSITORY` 環境變量的值。
>- **test with python**：運行 `python env.py` 命令來執行名為 `env.py` 的 Python 腳本。腳本的內容就是使用python語法讀取環境變數，且打印出來。
## 在Workflow中定義和使用Environment Variables
```yml
name: workflow_env
on: 
  workflow_dispatch:

env:
  DAY_OF_WEEK: Monday

jobs:

  build-in-env:
    runs-on: ubuntu-22.04
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: check default env
        run: env
      - name: print env
        run: echo $GITHUB_REPOSITORY
      - name: test with python
        run: python env.py
      - name: test env 
        run: echo "$Greeting $First_Name. Today is $DAY_OF_WEEK!"
  greeting_job:
    runs-on: ubuntu-latest
    env:
      Greeting: Hello
    steps:
      - name: "Say Hello Mona it's Monday"
        run: echo "$Greeting $First_Name. Today is $DAY_OF_WEEK!"
        env:
          First_Name: Mona
```
>[!info]
>這邊主要有兩個環境變數，
>1. 全局環境變數 
>	- DAY_OF_WEEK: Monday -> 對整個工作流程中的所有作業都有效
>2. 作業級環境變數
>	- Greeting: Hello ->在特定步驟內部定義環境變數，這些變數僅在該步驟內有效
## Repository Secrets
我們透過環境變亮可以對我們程序去設置一些參數，那這些參數有些是敏感，有些是不敏感的，像是密碼、token等等，那這些訊息我們也想透過環境變量去傳遞到我們的程序中，但如果我們透過環境變量設置，我們會直接把環境變量的值寫在 workflow 的 yml 文件中，這顯然是不安全的。

所以我們可以去 創建好的`repository > Setting > Secrets and variables > Actions > Secrets` 設置我們的環境變數。
```shell
name: workflow_env
on: 
  workflow_dispatch:

env:
  DB_U: test
  DB_P ${{ secrets.DB_PASSWARD }}

jobs:

  test-secrets:
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: check env u
        run: echo "u=${{ env.DB_U }}"
      - name: check env p
        run: echo "p=${{ secrets.DB_PASSWORD }}"
      - name: test with python
        run: python secrets.py
```
>[!info]
>這邊做了兩個實驗，使用 echo 去打印出訊息，以及使用 python 腳本運行方式打印，
>那我們會發現這兩種方式，Github Actions都幫你做了安全機制，使得打印出的數值幫會碼掉。**但還是要注意我們的密碼要設置複雜以確保安全**
## Repository Variables
一樣我們可以去創建好的`repository > Setting > Secrets and variables > Actions > Variables` 設置我們的變數。
```yml
name: workflow_env
on: 
  workflow_dispatch:

env:
  DB_U: ${{ vars.DB_USERNAME }}
  DB_P ${{ secrets.DB_PASSWARD }}

jobs:

  test-secrets:
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: check env u
        run: echo "u=${{ env.DB_U }}"
      - name: check env p
        run: echo "p=${{ secrets.DB_PASSWORD }}"
      - name: test with python
        run: python secrets.py
```
## 不同環境間的參數隔離
我們可以在創建好的`repository > Setting > Environments`，那當然然的我們也可以設置哪個參數只能要哪個分支上運行。

**注意這個功能只能是public repository以及付費用戶**
```yml
name: workflow_secrets
on: 
  workflow_dispatch:

jobs:
  test-secrets:
    environment: test
    env:
      DB_U: ${{ vars.DB_USERNAME }}
      DB_P: ${{ secrets.DB_PASSWORD }}
    runs-on: ubuntu-22.04
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: check env u
        run: echo "u=${{ env.DB_U }}"
      - name: check env p
        run: echo "p=${{ secrets.DB_PASSWORD }}"
      - name: test with python
        run: python secrets.py
  prod-secrets:
    environment: prod
    env:
      DB_U: ${{ vars.DB_USERNAME }}
      DB_P: ${{ secrets.DB_PASSWORD }}
    runs-on: ubuntu-22.04
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: check env u
        run: echo "u=${{ env.DB_U }}"
      - name: check env p
        run: echo "p=${{ secrets.DB_PASSWORD }}"
      - name: test with python
        run: python secrets.py
```
>[!info]
>看到 jobs 中的 environment 標籤: 這就是代表在 Github Action 中設置區分環境的名稱。
>

