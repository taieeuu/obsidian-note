---
Date: 2024-06-29
---
## 概念
`Middleware`是一個函數，他在每個請求被特定的路徑操作處理之前，以及在每個響應之後工作

![[Drawing 2024-06-29 13.17.19.excalidraw.png]]

>如果你使用了 `yield` 關鍵字依賴，依賴中的退出代碼將在執行中間件後執行。
>如果有任何後台任務（例如日誌記錄），它們將在執行中間件後運行。

要創建中間件你可以在函數的頂部使用裝飾器 `@app.middleware('http')`
中間件參數接收如下參數
## Demo
```python
from fastapi import FastAPI, Request
import time

app = FastAPI()

@app.middleware("http")
async def m2(request: Request, call_next):
    # 請求
    print('m2 request')
    response = await call_next(request)
    # 響應
    print('m2 response')
    return response

@app.middleware("http")
async def m1(request: Request, call_next):
    # 請求
    print('m1 request')
    response = await call_next(request)
    # 響應
    print('m1 response')
    return response

# Basic route
@app.get("/user")
async def read_root():
    print('get user函數執行')
    return {"message": "Hello World"}

# Another route with path parameter
@app.get("/items/{item_id}")
async def read_item(item_id: int):
    print('get items函數執行')
    return {"item_id": item_id, "name": f"Item {item_id}"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000, reload=True)
```
>[!info]
>我們去 `localhost:8000/docs` 執行 user 的這隻 api ，然後你會在終端機發現他的輸出會是 m1 request -> m2 request -> get user函數執行 -> m2 response -> m1 response，也就是我們上述觀念所提到的。
>**中間件的程式碼越下面他就會是執行的第一個越上面就是執行的最後一個。**

在中間件中，你會發現有一個`call_next`函數，讓我們來介紹一下。
### call_next
`call_next` 是一個異步函數，代表請求處理的下一個階段。當你在中間件中調用 `call_next(request)` 時，請求會傳遞到下一個中間件或最終的路由處理器。如果有多個中間件，它們會按照定義的順序依次執行。

以上述的 Demo 範例說明。
- **請求進入第一個中間件 (`m1`)**：
    - 打印 `m1 request`。
    - 調用 `call_next(request)`，這會把請求傳遞給下一個中間件或路由。
- **請求進入第二個中間件 (`m2`)**：
    - 打印 `m2 request`。
    - 調用 `call_next(request)`，這會把請求傳遞給最終的路由處理器。
- **請求到達路由處理器**：
    - 根據請求的路徑，例如 `/user` 或 `/items/{item_id}`，執行對應的路由處理函數，並打印相應的消息 (`get user函數執行` 或 `get items函數執行`)。
- **響應返回第二個中間件 (`m2`)**：
    - 路由處理器返回響應。
    - `m2` 中間件收到響應，打印 `m2 response`。
    - `m2` 中間件返回響應給第一個中間件 (`m1`)。
- **響應返回第一個中間件 (`m1`)**：
    - `m1` 中間件收到響應，打印 `m1 response`。
    - `m1` 中間件返回最終響應給客戶端。
## How to apply ?
範例:

1、我們可以在 m2 函數中，對 client 請求加上 header ，如下
```python
@app.middleware("http")
async def m2(request: Request, call_next):
    # 請求
    print('m2 request')
    response = await call_next(request)
    # 響應
    response.headers["X-Time"] = time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())
    response.headers['author'] = 'TaiChungMin'
    print('m2 response')
    return response
```
![[Pasted image 20240629144428.png]]
2、黑名單
```python
@app.middleware("http")
async def m1(request: Request, call_next):
    # 請求
    print('m1 request')
    if request.client.host in ['127.0.0.1']:
        return Response(status_code=400, content="Not allowed")
    response = await call_next(request)
    # 響應
    print('m1 response')
    return response
```
![[Pasted image 20240629144934.png]]
