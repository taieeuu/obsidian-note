---
Date: 2024-06-08
tags:
  - devops
  - docker
---
我們可以使用 Github Action 來進行自動化，只要 push 至 Github 上後，就可以自動更新至 Dockerhub
```yml
name: Docker image build and push

on: [push]

jobs:
  Docker-Build-Push:
    runs-on: ubuntu-latest
    steps:
    - name: Check out repository code
      uses: actions/checkout@v2
    - name: docker-image build
      run: docker build -t xiaopeng163/flask-redis:latest .
    - name: list docker image
      run: docker image ls
    - name: dockerhub login
      run: docker login -u ${{ secrets.DOCKERHUB_USERNAME }} -p ${{ secrets.DOCKERHUB_PASSWORD }}
    - name: push to dockerhub
      run: docker image push xiaopeng163/flask-redis:latest

```
>[!info]
>這邊的${{ secrets.DOCKERHUB_USERNAME }} 可以至 Github 中的 Secrete來進行添加。

## Github Action 佈署多架構
我們可以使用 Github Action 所提供的 Marketplace 中搜尋 docker build ，我們選擇 QEMU 使用他的代碼進行修改成自己要的。
```yml
name: ci

on:
  push:

jobs:
  qemu:
    runs-on: ubuntu-latest
    steps:
      -
        name: Set up QEMU
        uses: docker/setup-qemu-action@v3
	  -
		name: build and push
		run: /
			docker login -u ${{ secrets.DOCKERHUB_USERNAME }} -p ${{ secrets.DOCKERHUB_PASSWORD }}
			docker buildx ls
			docker buildx build --push --platform linux/arm/v7,linux/arm64/v8,linux/amd64 -t xiaopeng123/flask-redis:latest
```

我們可以發現在 Marketplace 有一個官方的 build and push docker images，我們使用這個來改進我們的code
```yml
name: ci

on:
  push:

jobs:
  qemu:
    runs-on: ubuntu-latest
    steps:
      -
        name: Set up QEMU
        uses: docker/setup-qemu-action@v3
      -
        name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      -
        name: Build and push
        uses: docker/build-push-action@v5
        with:
          push: true
          tags: user/app:latest
```
