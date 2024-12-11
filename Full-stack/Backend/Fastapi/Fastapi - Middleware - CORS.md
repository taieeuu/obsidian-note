---
Date: 2024-06-29
---
## CORS Demo
我們有一個 html 檔案，裡面有一個文字點擊他會發送`127.0.0.1:8000/user`請求，使用他進行 CORS Demo，我們使用雙擊點開 html 。

html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.js"></script>
</head>
<body>
    <p>hello world</p>
    <script>
        $("p").click(function(){
            $.ajax({
                url: "http://127.0.0.1:8000/user",
                success: function(res){
                    console.log(res);
                    console.log(res.user);
                }
            })
        });
    </script>
</body>
</html>
```

python
```python
from fastapi import FastAPI, Request

app = FastAPI()

@app.middleware("http")
async def MyCORSMiddleware(request: Request, call_next):
    print('middleware函數執行')
    response = await call_next(request)
    # response.headers["Access-Control-Allow-Origin"] = "*"
    return response

@app.get("/user")
async def read_root():
    print('get user函數執行')
    return {"message": "Hello World"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000, reload=True)
```
>[!info]
>雙擊點開 html 檔案點擊了文字，會發現瀏覽器幫我們封鎖了，**已封鎖跨來源請求**，原因是因為: 由於瀏覽器的同源政策（Same-Origin Policy, SOP）引起的。SOP 是一種安全機制，用來防止不同來源的資源之間相互訪問，從而保護用戶的數據安全。當你嘗試在本地文件系統中打開 HTML 檔案並點擊文字發送跨來源請求時，瀏覽器會封鎖這些請求，並顯示 "已封鎖跨來源請求" 的錯誤信息。

所以我們可以在 中間件 加上 `response.headers["Access-Control-Allow-Origin"] = "*"`來使他運作。

那其實 Fastapi 有自帶 CORS 的 Middleware 來給我們提供更複雜且方便的配置。
## Fastapi Middleware CORS
```python
from fastapi import FastAPI, Request
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

origins = [
    "http://localhost:8080",
]

app.middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/user")
async def read_root():
    print('get user函數執行')
    return {"message": "Hello World"}
if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000, reload=True)
```

