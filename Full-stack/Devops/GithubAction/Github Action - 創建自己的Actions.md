---
Date: 2024-06-10
---
>[!info]

## 創建本地 composite actions
我們可以把先前寫好的 Github Actions 中的 workflow 把它包成 github action 中的 composite action，讓你可以重複使用，或是你想分享到 Marketplace。

先前的
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
      #   name: set up py
      #   with:
      #     python-version: 3.8
      - name: install requirements
        run: pip install -r requirements.txt
      - name: run py test
        id: run-py-test
        run: pytest
```

首先我們要先在 `.github` 創建 `action` 然後裡面寫你的資料夾名稱且命名為`action.yml`

這個文件就是我們所創建好的 Github Actions

我們根據[官方文檔](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action)的 Sample Code 來改寫

以下為Sample Code
```yml
name: 'Hello World'
description: 'Greet someone'
inputs:
  who-to-greet:  # id of input
    description: 'Who to greet'
    required: true
    default: 'World'
outputs:
  random-number:
    description: "Random number"
    value: ${{ steps.random-number-generator.outputs.random-number }}
runs:
  using: "composite"
  steps:
    - name: Set Greeting
      run: echo "Hello $INPUT_WHO_TO_GREET."
      shell: bash
      env:
        INPUT_WHO_TO_GREET: ${{ inputs.who-to-greet }}

    - name: Random Number Generator
      id: random-number-generator
      run: echo "random-number=$(echo $RANDOM)" >> $GITHUB_OUTPUT
      shell: bash

    - name: Set GitHub Path
      run: echo "$GITHUB_ACTION_PATH" >> $GITHUB_PATH
      shell: bash
      env:
        GITHUB_ACTION_PATH: ${{ github.action_path }}

    - name: Run goodbye.sh
      run: goodbye.sh
      shell: bash
```

這是我們在`.github/actions/docker/action.yml`，照上面範例進行修改，與先前的 workflow 進行修改。
```yml
name: 'build docker image'
description: 'build docker image'
inputs:
  docker_hub_user:
    description: 'Docker Hub user'
    required: true
  docker_hub_token:
    description: 'Docker Hub token'
    required: true
  image_name:
    description: 'Docker image name'
    required: true
    
runs:
  using: 'composite'
  steps:
- name: docker login
  run: |
    echo ${{ inputs.docker_hub_token }} > password.txt
    cat password.txt | docker login --username ${{ inputs.docker_hub_user }} --password-stdin
  shell: bash
- name: build image
  run: |
    docker image build -t ${{ inputs.docker_hub_user }}/{{ inputs.image_name }} .
  shell: bash
- name: push image
  run: |
    docker image push ${{ inputs.docker_hub_user }}/{{ inputs.image_name }}:latest
  shell: bash

```

Github Actions 的 Workflow
```yml
name: build-docker-image
on:
  workflow_dispatch:
  push:
    branches:
      - 'master'

jobs:
  build-docker-image:
    runs-on: ubuntu-22.04

    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: build docker image
        uses: ./.github/actions/docker
        with:
          docker_hub_user: ${{ secrets.DOCKER_HUB_USER }}
          docker_hub_token: ${{ secrets.DOCKER_HUB_TOKEN }}
          image_name: fastapi-mongo-demo:latest
```
>[!info]
>- **名稱**：`checkout code`
>- **使用的動作**：`actions/checkout@v3`
>- **作用**：檢出存儲庫的最新代碼，使其可供後續步驟使用。
>- ---
>- **名稱**：`build docker image`
>- **使用的動作**：`./.github/actions/docker`
>	- 這表明使用的是存儲庫中的一個自定義動作，位置為 `.github/actions/docker`。
>- **參數 (`with`)**：
>	- `docker_hub_user`：從 GitHub Secrets 中獲取 Docker Hub 用戶名。
>	- `docker_hub_token`：從 GitHub Secrets 中獲取 Docker Hub 訪問令牌。
>	- `image_name`：設置 Docker 映像名稱為 `fastapi-mongo-demo:latest`。

## 建立一個可以分享的action
首先創建一個 Github Repository 且使用 Github 中的 CodeSpaces 在最外層創建一個`action.yml`，然後將之前創建的 docker 的 github action 複製到 CodeSpaces 中的 `action.yml`推至github。

如果要分享的話可以在推至 Github Marketplaces ，現在我們也可以使用在本地端 call Github 中的 actions !
```yml
name: build-docker-image
on:
  workflow_dispatch:
  push:
    branches:
      - 'master'

jobs:
  build-docker-image:
    runs-on: ubuntu-22.04

    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: build docker image
        uses: <你的github中的actions倉庫位置>@<分支或是版本>
        with:
          docker_hub_user: ${{ secrets.DOCKER_HUB_USER }}
          docker_hub_token: ${{ secrets.DOCKER_HUB_TOKEN }}
          image_name: fastapi-mongo-demo:latest
```
##  Docker container actions
**注意只能使用linux，如果是windows則無法**
我們使用這個 Action : [https://github.com/xiaopeng163/docker-container-actions](https://github.com/xiaopeng163/docker-container-actions) 
```yml
name: 'test docker container aciton '

on: workflow_dispatch

jobs:
  hello_world_job:
	runs-on: ubuntu-latest
	name: Ajob to say hello
	steps:
	  - name: hello world action step
	  - id: hello
	    uses: xiaopeng163/docker-container-actions@master
	    with: 
	      who-to-greet: 'GITHUB Docker container actions'
	    name: Get the output time
	    run: echo 'The time was ${{ steps.hello.outputs.time }}'
```
>[!info]
>**名稱**: hello world action step
>**id**: 可以在後續步驟中引用。
>**uses**:用了一個名為 `xiaopeng163/docker-container-actions` 的 GitHub Action，分支是 `master`。
>**參數 (`with`)**：傳遞一個 who-to-greet 參數給到了 docker-container-aciton ，因為她的 inputs 中有這個參數將他傳給他。

## Create JavaScript Actions
可以參考Github Actions 中的 Create JavaScript Actions 。
[官方文檔](https://docs.github.com/en/actions/creating-actions/creating-a-javascript-action)