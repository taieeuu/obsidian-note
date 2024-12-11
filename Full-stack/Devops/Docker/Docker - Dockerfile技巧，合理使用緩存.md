---
Date: 2024-06-01
tags:
  - docker
  - devops
---
### 合理使用缓存
以下述例子說明
Python file
```python
from flask import Flask

app = Flask(__name__)


@app.route('/')
def hello_world():
    return 'Hello, World!'
```
Dockerfile
```dockerfile
FROM python:3.9.5-slim

COPY app.py /src/app.py

RUN pip install flask

WORKDIR /src
ENV FLASK_APP=app.py

EXPOSE 5000

CMD ["flask", "run", "-h", "0.0.0.0"]
```

我們重新 build 會發現他會比原先 build 的時候還要快，因為Docker 在構建映像時，會將每個 `Dockerfile` 指令轉換成一層。當你重建映像時，Docker 會檢查每一層的指令是否已經存在於緩存中。

那假設我們上述的 return 'Hello, World!' 改成 'hello' 的話，我們會發現它會不使用Cached也就是重新的執行，因為 Docker 構建映像是按照 Dockerfile 中的指令順序從上到下執行的，一旦某一層發生變化，這一層及其之後的所有層都會重新構建。因此，將不經常變動的指令（如安裝包的命令）放在 Dockerfile 的上方，而將經常變動的指令（如添加源代碼）放在下方。