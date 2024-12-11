---
Date: 2024-08-20
---
Web 在線聊天的實現方法有多種，每一種時線方法的基本原理各不相同，如下 : 

- `Ajax` : 通過 Ajax 實現網頁與服務器的無刷新交互，在網頁上每隔一段時間就通過 `Ajax` 從服務器中獲取數據，然後將數據更新並顯示在網頁上。這種方法簡單明瞭，但缺點是實時性不高。
- `Comet (Pushlet)` : `Comet` 是一種 Web 應用架構，服務器以一部方式向瀏覽器推送數據，無須瀏覽器發送。`Comet` 架構非常適合事件趨動的 Web 應用，以及對交互性和實時性要求較高的應用，如股票交易行情分析、聊天室和 Web 在線遊戲等。
- `XMPP` 協議 : `XMPP` (可擴展消息處理現場協議) 視基於 `XML` 的協議，是專為即時通新系統設計的通新協議，用於即時霄西以及在線現場探測。這個協議允許用戶向其他用戶發送即時消息。
- `Flash` 的 `XmlSocket` : `Flash Media Server` 是一個強大流媒體服務器，他基於 `RTMP` 協議，提供穩定的流媒體交互功能。此外，它內置的遠程共享對象 (Shared Object) 機制，可以讓瀏覽器創建並連接服務器的遠程共享對象。\
- `WebSocket` 協議 : `WebSocket` 是通過單個 `TCP` 連接提供全雙工 (雙向通信)，通信信到的計算機通信協議，可以在瀏覽器和服務器之間進行雙向通信。允許多個用戶連接到同一個實時服務器，並通過 `API` 進行通信且立即獲得響應。 `WebSocket` 不僅適用於聊天 or 消息傳遞應用程序。如體育更新、股票行情...。
### #### 為什麼使用 Django Channels？
Django 是一個同步框架，通常用於處理 HTTP 請求-回應模型。然而，對於即時通信，我們需要處理雙向通信，這時 WebSocket 是更好的選擇。Django Channels 擴展了 Django，使其能夠支持 WebSocket 等協議，允許我們在 Django 中構建非同步應用程式。

將 Channels 添加到 Django 的 `INSTALLED_APPS` 中：

```python
# myproject/settings.py

INSTALLED_APPS = [
    # 其他已安裝的應用
    'channels',
]

# 配置 Channels 的層級路由
ASGI_APPLICATION = 'myproject.asgi.application'
```
### 設置 ASGI 應用程式
與傳統的 WSGI 配置不同，Channels 使用 ASGI 作為 Django 的協程服務介面。你需要創建一個 `asgi.py` 文件，與 `settings.py` 同級，並配置 Channels 層級路由。

```python
# myproject/asgi.py

import os
from channels.auth import AuthMiddlewareStack
from channels.routing import ProtocolTypeRouter, URLRouter
from django.core.asgi import get_asgi_application
import myapp.routing

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

application = ProtocolTypeRouter({
    "http": get_asgi_application(),
    "websocket": AuthMiddlewareStack(
        URLRouter(
            myapp.routing.websocket_urlpatterns
        )
    ),
})
```
### 創建 WebSocket 路由
在你的應用程式中創建一個 `routing.py` 文件，並定義 WebSocket 的路由：

```python
# myapp/routing.py

from django.urls import path
from . import consumers

websocket_urlpatterns = [
    path('ws/chat/<str:room_name>/', consumers.ChatConsumer.as_asgi()),
]
```
### 創建 Consumers
在 Django Channels 中，`Consumers` 是用來處理 WebSocket 連接的。創建一個 `consumers.py` 文件，並定義一個基本的聊天 Consumer：

```python
# myapp/consumers.py

import json
from channels.generic.websocket import AsyncWebsocketConsumer

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.room_name = self.scope['url_route']['kwargs']['room_name']
        self.room_group_name = f'chat_{self.room_name}'

        # 加入房間群組
        await self.channel_layer.group_add(
            self.room_group_name,
            self.channel_name
        )

        await self.accept()

    async def disconnect(self, close_code):
        # 離開房間群組
        await self.channel_layer.group_discard(
            self.room_group_name,
            self.channel_name
        )

    # 接收來自 WebSocket 的訊息
    async def receive(self, text_data):
        text_data_json = json.loads(text_data)
        message = text_data_json['message']

        # 發送訊息到房間群組
        await self.channel_layer.group_send(
            self.room_group_name,
            {
                'type': 'chat_message',
                'message': message
            }
        )

    # 接收來自房間群組的訊息
    async def chat_message(self, event):
        message = event['message']

        # 發送訊息到 WebSocket
        await self.send(text_data=json.dumps({
            'message': message
        }))
```
### 配置 Channel Layer
Channel Layer 是用來在不同的消費者之間傳遞訊息。在開發過程中，可以使用 Redis 作為 Channel Layer 的後端。安裝 Redis 並配置 Channels 使用 Redis：

```sh
pip install channels_redis
```

然後在 `settings.py` 中進行配置：

```python
# myproject/settings.py

CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels_redis.core.RedisChannelLayer',
        'CONFIG': {
            "hosts": [('127.0.0.1', 6379)],
        },
    },
}
```
### 創建聊天頁面
在視圖和模板中創建一個簡單的聊天介面，讓用戶可以輸入訊息並即時看到其他用戶發送的訊息。
###### 視圖
```python
# myapp/views.py

from django.shortcuts import render

def index(request):
    return render(request, 'chat/index.html')

def room(request, room_name):
    return render(request, 'chat/room.html', {
        'room_name': room_name
    })
```
###### URL 路由
```python
# myapp/urls.py

from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
    path('chat/<str:room_name>/', views.room, name='room'),
]

```
###### index.html
```python
<!DOCTYPE html>
<html>
<head>
    <title>聊天室</title>
</head>
<body>
    <h1>歡迎來到聊天室</h1>
    <form action="{% url 'room' room_name=room_name %}">
        <label for="room-name-input">輸入聊天室名稱:</label>
        <input id="room-name-input" type="text" name="room_name">
        <input type="submit" value="進入聊天室">
    </form>
</body>
</html>
```
###### room.html
```python
<!DOCTYPE html>
<html>
<head>
    <title>聊天室</title>
</head>
<body>
    <h2>房間名稱: {{ room_name }}</h2>
    <div id="chat-log"></div>
    <input id="chat-message-input" type="text" size="100">
    <input id="chat-message-submit" type="button" value="發送">

    <script>
        const roomName = "{{ room_name }}";
        const chatSocket = new WebSocket(
            'ws://' + window.location.host + '/ws/chat/' + roomName + '/'
        );

        chatSocket.onmessage = function(e) {
            const data = JSON.parse(e.data);
            document.querySelector('#chat-log').innerHTML += ('<br>' + data.message);
        };

        chatSocket.onclose = function(e) {
            console.error('Chat socket closed unexpectedly');
        };

        document.querySelector('#chat-message-input').focus();
        document.querySelector('#chat-message-input').onkeyup = function(e) {
            if (e.keyCode === 13) {  // Enter key
                document.querySelector('#chat-message-submit').click();
            }
        };

        document.querySelector('#chat-message-submit').onclick = function(e) {
            const messageInputDom = document.querySelector('#chat-message-input');
            const message = messageInputDom.value;
            chatSocket.send(JSON.stringify({
                'message': message
            }));
            messageInputDom.value = '';
        };
    </script>
</body>
</html>
```
### 測試
```sh
python manage.py runserver
```