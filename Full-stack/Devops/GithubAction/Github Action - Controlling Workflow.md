---
Date: 2024-06-09
---
## Workflow Execution
![[Controlling Workflow.png]]
>[!info]
Runner可以是並聯也可以是串聯。
## Job 依賴關係
```yml
name: Example Workflow

on: push

jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v2
    - name: Run Job 1
      run: echo "This is Job 1"

  job2:
    runs-on: ubuntu-latest
    needs: job1
    steps:
    - name: Run Job 2
      run: echo "This is Job 2"

  job3:
    runs-on: ubuntu-latest
    needs: [job1, job2]
    steps:
    - name: Run Job 3
      run: echo "This is Job 3"
```
>[!info]
>- job1 是第一個執行的工作，因為沒有任何依賴。
>- job2 依賴 job1，因此 job1 完成後才會執行 job2。
>- job3 依賴 job1 和 job2，所以 job1 和 job2 完成後才會執行 job3。
## Job 狀態檢查
```yml
name: demo

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
      - name: install requirements
        run: pip install -r requirements.txt
      - name: run py test
        run: pytest --cov-report html:htmlcov --cov-report term --cov=project tests
      - name: upload test reports
        uses: actions/upload-artifact@v3
        with:
          name: test-report
          path: htmlcov/

  deploy:
    runs-on: ubuntu-22.04
    if: ${{ failure() }}
    needs: test-code
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: deploy code
        run: echo "deploy........."
```
>[!info]
>- deploy Job 依賴 test-code，只有在 test-code 失敗時才會執行，因為使用了 if: ${{ failure() }} 條件。 ps. 還有像是 always()、success()等操作。
>- `if: ${{ failure() }}` 我們可以寫成 `failure()` 

上述的例子在 Job 之間使用 if 來判斷，**那當然的我們也可以在 Step 中使用**。
```yml
name: demo

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
      - name: install requirements
        run: pip install -r requirements.txt
      - name: run py test
        run: pytest --cov-report html:htmlcov --cov-report term --cov=project tests
      - name: upload test reports
	    if: failure()
        uses: actions/upload-artifact@v3
        with:
          name: test-report
          path: htmlcov/

  deploy:
    runs-on: ubuntu-22.04
    if: ${{ failure() }}
    needs: test-code
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: deploy code
        run: echo "deploy........."
```
>[!info]
>在 upload test reports 中
>- `if: failure()`: 表示只有在前面的步驟失敗時才會執行這一步。意味著前面只要有一個失敗就會執行。

那如果我們要特別的 step 失敗才執行呢 ?

我們可以在指定該 step 設置 id ，讓之後需要執行的 step 去使用
```yml
name: demo

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
      - name: install requirements
        run: pip install -r requirements.txt
      - name: run py test
      - if: run-py-test
        run: pytest --cov-report html:htmlcov --cov-report term --cov=project tests
      - name: upload test reports
	    if: failure() && steps.run-py-test.outcome == 'failure'
        uses: actions/upload-artifact@v3
        with:
          name: test-report
          path: htmlcov/

  deploy:
    runs-on: ubuntu-22.04
    if: ${{ failure() }}
    needs: test-code
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: deploy code
        run: echo "deploy........."
```
>[!info]
>`Upload test reports` 步驟的 if 條件更新為 if: failure() && steps.run_py_test.outcome == 'failure'，確保只有在 run_py_test 步驟失敗時才會上傳測試報告。**更多的設置可以參考官方文檔。**
## Matrix的使用
假設我們需要使用不同 Python 版本，但 steps、job都一樣的話我們就可以使用 Matrix。

```yml
name: demo

on:
  workflow_dispatch:
  push:
    branches:
      - 'master'
      
jobs:
  test-code:
    strategy:
	  matrix:
		version:['3.8','3.9','3.10']
    runs-on: ubuntu-22.04
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: set up py
	    with:
	      python-version: ${{ matrix.version }}
      - name: install requirements
        run: pip install -r requirements.txt
      - name: run py test
      - if: run-py-test
        run: pytest --cov-report html:htmlcov --cov-report term --cov=project tests
      - name: upload test reports
	    if: failure() && steps.run-py-test.outcome == 'failure'
        uses: actions/upload-artifact@v3
        with:
          name: test-report-${{ matrix.version }}
          path: htmlcov/

  deploy:
    runs-on: ubuntu-22.04
    if: ${{ failure() }}
    needs: test-code
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: deploy code
        run: echo "deploy........."
```
>[!info]
>由於操作一樣，所以我們可以使用 Matrix 的方式去執行，要注意的是 test-report 部分，如果沒有添加後面版本參數，最終輸出只會有一個檔案，因為檔案名稱一樣的關係。

那如果說我們作業系統也要使用不同的呢，也就是說會有兩層循環。
```yml
name: demo

on:
  workflow_dispatch:
  push:
    branches:
      - 'master'
      
jobs:
  test-code:
    runs-on: ubuntu-22.04
    strategy:
	  matrix:
		version:['3.8','3.9','3.10']
		os: [ubuntu-22.04, ubuntu-20.04]
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: set up py
	    with:
	      python-version: ${{ matrix.version }}
      - name: install requirements
        run: pip install -r requirements.txt
      - name: run py test
      - if: run-py-test
        run: pytest --cov-report html:htmlcov --cov-report term --cov=project tests
      - name: upload test reports
	    if: failure() && steps.run-py-test.outcome == 'failure'
        uses: actions/upload-artifact@v3
        with:
          name: test-report-${{ matrix.version }}-${{ matrix.os }}
          path: htmlcov/

  deploy:
    runs-on: ubuntu-22.04
    if: ${{ failure() }}
    needs: test-code
    steps:
      - name: checkout code
        uses: actions/checkout@v3
      - name: deploy code
        run: echo "deploy........."
```