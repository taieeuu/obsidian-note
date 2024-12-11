---
Date: 2024-06-09
---
> [!info]
> 項目資源: https://drive.google.com/file/d/1KJ0rPeYYcDZD3POcN122K4QVVjVXJcyA/view?usp=drive_link
> 官方文檔參考:https://docs.github.com/en/actions/using-containerized-services/about-service-containers

項目資源是使用 fastapi + mongodb 進行運行，裡面有提供docker-compose、docker，藉由這些去建造 Github Action
## 在 Runner 使用 Docker
我們可以看到項目資源中，workflow是需要一個容器的，所以 Runner 中必須安裝 docker，那其實我們可以去官方文檔中，看到她的 ubuntu-22.04 是有安裝 docker 的。

```yml
name: testing
on:
  workflow_dispatch:
  push:
    branches:
      - 'master'

jobs:
  test-code:
    runs-on: ubuntu-22.04
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: actions/setup@v3
        with:
          python-version: 3.8
      - name: install requirements
        run: pip install -r requirements.txt
      - name: create mongodb container
        run: |
          docker run -d -p 27017:27017 --name some-mongo\
          -e MONGO_INITDB_ROOT_USERNAME=root\
          -e MONGO_INITDB_ROOT_PASSWORD=example\
          mongo
      - name: run py test
        id: run-py-test
        run: pytest
```
>[!info]
>- **工作流程名稱：** `name: testing`
>	- 這個工作流程名稱是 `testing`。
>- **觸發條件：** `on:`
>	- `workflow_dispatch:` 允許手動觸發工作流程。
>	- `push:` 當推送到 `master` 分支時觸發工作流程。
>- **作業：** `jobs:`  
>	- 定義一個名為 `test-code` 的作業。
>- **執行環境：** `runs-on: ubuntu-22.04`  
>	- 此作業將在 Ubuntu 22.04 上運行。
>- **步驟：** `steps:`
>	- `checkout code`:
>		- 使用 `actions/checkout@v3` 來檢出存儲庫的代碼。
>	- `setup python`:
>		- 使用 `actions/setup@v3` 並設置 Python 版本為 3.8。
>	- `install requirements`:
>		- 運行命令 `pip install -r requirements.txt` 安裝依賴。
>	- `create mongodb container`:
>		- 運行一個命令來創建並啟動 MongoDB Docker 容器，設置用戶名為 `root` 和密碼為 `example`，暴露端口 27017。
>	- `run py test`:
>		- 運行 `pytest` 來執行 Python 測試。

通過自己嘗試，可以看到在 Github Action 中的 workflow 裡面，直接使用 docker 原始命令去創建容器。
## 使用 Service Container
在項目資源中，我們可以看到裡面有一個 docker-compose 裡面有可以定義像是 service ，那 Github Action 中也有提供，我們可以透過這種方式創建 container 。

也就是說下密的部份我們可以通過 service container 的語法，來替換掉。
```yml
  - name: create mongodb container
	run: |
	  docker run -d -p 27017:27017 --name some-mongo\
	  -e MONGO_INITDB_ROOT_USERNAME=root\
	  -e MONGO_INITDB_ROOT_PASSWORD=example\
	  mongo
```

詳細可以查看到我們上述的官方文檔，
```yml
name: testing
on:
  workflow_dispatch:
  push:
    branches:
      - 'master'

jobs:
  test-code:
    runs-on: ubuntu-22.04
    services:
      mongo:
        image: mongo:6.0.2
        env:
          MONGO_INITDB_ROOT_USERNAME: root
          MONGO_INITDB_ROOT_PASSWORD: example
        ports:
          - 27017:27017
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        name: set up py
        with:
          python-version: 3.8
      - name: install requirements
        run: pip install -r requirements.txt
      - name: run py test
        id: run-py-test
        run: pytest
```
>[!info]
>繼承上述的代碼，我們替換掉了使用 docker 命令的方式創建 mongodb 容器，我們改用 Github Action 所提供的 service container，這就有點像使用 docker-compose 來定義一個容器的概念，是可以完全 copy 你之前所定義的 docker-compose 止不過語法要稍微修改一下。
## 直接在container裡運行Job
在上述的代碼中，我們可以使用 services container 來連接 mongodb 來測試我們的 python 代碼，但我們這個測試運行是跑在我們的 Runner 上面的，不過實際上我們是可以讓 Steps 或是 Job 跑在我們的 Container 裡面的。

```yml
name: testing
on:
  workflow_dispatch:
  push:
    branches:
      - 'master'

jobs:
  test-code:
    runs-on: ubuntu-22.04
    container: python:3.8-slim
    env:
      MONGO_URL: ${{ secrets.MONGO_URL }}
    services:
      mongo:
        image: mongo:6.0.2
        env:
          MONGO_INITDB_ROOT_USERNAME: root
          MONGO_INITDB_ROOT_PASSWORD: example
        ports:
          - 27017:27017
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      # - uses: actions/setup-python@v4
      #   name: set up py
      #   with:
      #     python-version: 3.8
      - name: install requirements
        run: pip install -r requirements.txt
      - name: run py test
        id: run-py-test
        run: pytest
```
>[!info]
>繼承上述的代碼，這次我們將原本在 steps 中的 python 環境創造成一個容器，也就是說這裡有兩個容器，可以把他想像成一個是直接從 dockerhub 上抓取下來直接運行成 container 另一個則是使用 docker-compose 來定義一個 mongodb 的容器，那容器之間的通性就如同在 docker 中一樣了。

