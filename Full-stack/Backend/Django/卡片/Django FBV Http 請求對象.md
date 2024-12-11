---
Date: 2024-08-25
---
### 處理不同的HTTP方法
在FBV中，可以根據請求方法來執行不同的邏輯。常見的HTTP方法有GET、POST、PUT、DELETE等。
#### Http 請求方式

| HTTP 方法     | 用途                      | 特點                                        |
| ----------- | ----------------------- | ----------------------------------------- |
| **GET**     | 請求從伺服器獲取數據              | 安全（safe）且幂等（idempotent），不改變伺服器上的資源狀態      |
| **POST**    | 向伺服器提交數據，創建新資源或提交表單     | 可能改變伺服器上的資源狀態，通常不是幂等的                     |
| **PUT**     | 上傳數據至指定位置，更新或創建資源       | 幂等方法，多次執行效果相同                             |
| **DELETE**  | 請求伺服器刪除指定資源             | 幂等方法，多次執行效果相同                             |
| **HEAD**    | 請求HTTP標頭，不返回主體內容        | 用於檢查資源是否存在或檢查元數據，與GET類似但不返回數據內容           |
| **OPTIONS** | 查詢伺服器支持的通信選項            | 返回支持的HTTP方法列表，常用於CORS預檢請求                 |
| **PATCH**   | 部分更新資源                  | 用於局部修改資源，通常不是幂等的，但可以實現幂等                  |
| **TRACE**   | 回顯伺服器收到的請求，用於調試         | 用於診斷和調試，返回請求的內容                           |
| **CONNECT** | 用於建立隧道以便進行TCP通信（如HTTPS） | 用於在代理伺服器上建立與目標資源的隧道，通常用於HTTPS協議的SSL/TLS握手 |

對於 Django 來說，當他接收到 `Http` 請求之後，會根據 `Http` 請求攜帶請求參數以及請求訊息來創建一個 `WSGIRequest` 對象，並將它作為視圖的首個對象，這個參數通常寫為 `request`，該參數包含用戶的請求所有訊息。

P.S. :  `WSGIRequest` 對象既程並重寫類 `HttpRequest`。
###### 示例
```python
def my_view(request):
    if request.method == 'GET':
        return HttpResponse("This is a GET request")
```
### 上傳文件
#### 設置模型
首先，你需要在Django模型中定義一個字段來存儲上傳的文件。Django提供了`FileField`和`ImageField`兩種字段來處理文件和圖片。

```python
from django.db import models

class Document(models.Model):
    title = models.CharField(max_length=100)
    upload = models.FileField(upload_to='uploads/')  # 這裡設定文件上傳的路徑
    uploaded_at = models.DateTimeField(auto_now_add=True)
```
#### 創建表單
接下來，創建一個表單來處理文件上傳。你可以使用Django的`forms.ModelForm`來生成與模型對應的表單。

```python
from django import forms
from .models import Document

class DocumentForm(forms.ModelForm):
    class Meta:
        model = Document
        fields = ('title', 'upload')
```
#### 設置視圖處理文件上傳
```python
from django.shortcuts import render, redirect
from .forms import DocumentForm
from .models import Document

def upload_file(request):
    if request.method == 'POST':
        form = DocumentForm(request.POST, request.FILES)
        if form.is_valid():
            form.save()
            return redirect('upload_success')
    else:
        form = DocumentForm()
    return render(request, 'upload.html', {'form': form})
```

在這裡，我們檢查了請求方法，如果是`POST`，則表示用戶提交了文件，然後通過`form.is_valid()`來驗證表單並保存文件。`request.FILES`字典包含了所有上傳的文件。

#### ### 設置模板
你需要一個模板來顯示表單，並允許用戶選擇和上傳文件。

```django html
<!DOCTYPE html>
<html>
<head>
    <title>文件上傳</title>
</head>
<body>
    <h1>上傳文件</h1>
    <form method="POST" enctype="multipart/form-data">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">上傳</button>
    </form>
</body>
</html>
```

在這裡，`enctype="multipart/form-data"`是必須的，它告訴瀏覽器在發送表單時如何處理文件數據。

- **`<form method="POST" enctype="multipart/form-data">`** : 這個`<form>`標籤定義了一個表單，用來提交用戶輸入的數據。這裡有兩個重要的屬性：

	- **`method="POST"`**：這表示表單將使用`POST`方法提交數據。`POST`方法用於將數據發送到服務器，特別適用於處理文件上傳或更改服務器上的資源。
	- **`enctype="multipart/form-data"`**：這個屬性指定了表單的編碼類型，它告訴瀏覽器在提交表單時如何處理文件數據。對於文件上傳來說，這個屬性是必需的。

- **`{{ form.as_p }}`** : 這是一個Django模板語法，用來將表單中的字段以段落`<p>`標籤包裹的形式顯示出來。`form.as_p`表示使用段落來顯示每個表單字段及其相關的標籤和錯誤信息。