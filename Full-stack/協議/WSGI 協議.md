---
Date: 2024-08-27
---
`WSGI（Web Server Gateway Interface）`是一種**協議**。WSGI 定義了 web server 跟 python web application 之間溝通的規範，這樣的規範提供開發者很大的彈性，可以自由選擇不同的 web server 跟 python web application 去做搭配使用，只要是兩者都有符合 WSGI 就可以。WSGI 是由 PEP 333 提出的，並在 2010 年由 PEP 3333 更新。
### WSGI 的背景和目的
在 WSGI 標準提出之前，Python Web 框架和 Web 伺服器之間缺乏一個統一的接口，不同的框架和伺服器之間存在不兼容的問題。這使得開發者在選擇框架和伺服器時受到限制。WSGI 的出現解決了這一問題，它為 Web 框架和伺服器之間提供了一個標準化的通信接口，使得開發者可以自由選擇和組合不同的框架和伺服器，而不必擔心兼容性問題。
### WSGI 與 uWSGI
WSGI 跟 WSGI server 這兩個名詞常常會搞混，誤以為 WSGI 就是一種伺服器的名稱，其實 WSGI server 意思指的是遵照 WSGI 規範的 server 哦！像是 gunicorn, uwsgi 都是屬於 WSGI server 哦。
### WSGI 的工作原理
- WSGI 應用程序 : 
- WSGI 伺服器 : 
### WSGI 寫了些什麼？
WSGI 規格中的規定主要分為 2 端：

1. Application / Framework Side
2. Server / Gateway Side

如果要開發像 Flask, Django 之類的 Framework 或 Application, 只要遵守 Application / Framework Side 的規定就好。

如果要開發像 gunicorn, uWSGI 之類的 WSGI HTTP server, 則是遵守 Server / Gateway Side 的規定。
### 為什麼要用 WSGI?
在傳統的 web server 中，像是 nginx, apache 是無法去執行 Python web application 的。因此需要一個**能夠執行**的 web server 來達到這個目的，而這種 web server 也叫做 WSGI server。

所以 Python Web 應用程序通過 WSGI 協議運行，WSGI 伺服器（如 uWSGI）負責將應用程序連接到 Web 伺服器（如 Nginx 或 Apache），這些 Web 伺服器通常作為反向代理來管理請求和響應。
### #### WSGI伺服器
Python有多個流行的WSGI伺服器，例如：

- **Gunicorn**：一個基於預分配多個工作進程的高性能WSGI伺服器。
- **uWSGI**：一個多用途伺服器，可以用於運行WSGI應用，並且支持各種協議和功能。
- **Werkzeug**：Flask框架背後的WSGI工具包，內建一個簡單的WSGI伺服器，適合開發時使用。

你可以通過以下方式運行WSGI應用：

```bash
gunicorn myapp:simple_app
```