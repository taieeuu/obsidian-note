---
Date: 2024-05-25
---
Github Action 是一個可以幫我們執行==CI/CD的平台==，允許我們自動build、test、deployment pipeline，那在Github裡面我們把pipeline稱之為workflows，我們透過這個workflow自動化的去執行這些build、test、deployment也就是CI/CD裡面的工作。

所有的 github-action 都會在project_name/.github/workflows底下。
簡單的 sample github-action 
```yml
# This is a basic workflow to help you get started with Actions
name: CI

# Controls when the workflow will run
on: workflow_dispatch

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build_on_linux:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Runs a single command using the runners shell
      - name: Run a one-line script
        run: echo Hello, world!

      # Runs a set of commands using the runners shell
      - name: Run a multi-line script
        run: |
          echo Add other actions to build,
          echo test, and deploy your project.
          date
  build_on_windows:
    runs-on: windows-latest
    if: ${{ success() }}
    needs: [build_on_linux]
    steps:
      - name: run multi-line script
        run: |
          echo "hello github actions"
          Get-Date
```

> [!info]
> name : 定義
## Runer

串型兩個jobs，如果前面的job失敗則不執行下一個job。
```yaml

```


## Workflow Event 
觸發我們workflow的一個trigger，有非常多種的方式，但大體上分為兩大類。
- Repository related : 與代碼庫相關
	- push
	- pull_request
- Others
	- workflow_dispatch : 手動
	- schedule : 排程

```yml
name: pytest
on: [push, workflow_dispatch]
```

這樣就可以觸發在push 以及 手動 觸發workflows了，但要注意一點: 這邊的 push 其他的分支也會進行。

### Event activity types & filters
在 pull_request 各個不同狀態執行
```yml 
on:
	pull_request:
		types: [opened, reopened]
```

只希望在該分支上面觸發workflow

```yml 
on:
	push:
		branch: 
			- master
	workflow_dispatch:
```

### Pull_request
### Schedule
Github action 中的 schedule 使用的是 linux 中的 任務管理 cron 方式來執行，所以語法也是一樣。 
```yml
name: pytest
on:
	push:
		branches:
			- master
	workflow_dispatch:
	pull_request:
		type:[opened]
	schedule:
		- cron: '*/5 * * * *'
jobs: 
	....
```
### Skipping
假設我們 `commit` 一個 md 檔案，我們會不希望也觸發我們的 github action，所以通常我們會略過他，我們可以使用下面的方式，像是在 commit 時，添加 `[skip ci]`等等的訊息，這樣github action 就會知道了，詳細多變化可參考 Github Action 官方文件。
