---
Date: 2024-08-11
tags:
  - Python
  - Django
---
### 安裝
```shell
pip install Django
```
#### 查看版本
```python
python # 進入python交互解釋器

import django

django.__version__
```
### 創建項目
```sh
django-admin startproject <project_name>
```

項目中會包含 `<prject_name>` 文件夾和 `manage.py` 文件，而 MyDjango 文件夾中又包含 5 個 .py 文件。說明如下

- `manage.py `: 命令行工具，內置多種方式與項目進行交互。可切換置 `<prject_name>` 底下，輸入 `python manage.py help`，查看工具的指令訊息。
- `__init__.py` : 初始化文件，一般無須修改。
- `asgi.py` : 開啟一個 `ASGI.py` 服務，`ASGI` 是異步網關協議接口。
- `settings.py` : 項目配置文件，項目的所有功能都需要在該文件中進行配置。
- `urls.py` : 項目的路由設置，設置網站的具體網址內容。
- `wsgi.py` : 全名為 `Python Web Server Gateway Interface`、即 Python 服務器網關接口，是 Python 與 Web 服務器之間的接口，用於 Django 項目在服務器上部署和上線，一般無需修改。
### 創建項目應用
```sh
python manage.py startapp <app_name>
```

`<app_name>` 資料夾底下，會有多個 .py 文件和 1 個 `migrations` 文件夾，說明如下。

- `migrations` : 用於生成數據遷移文件，通過數據遷移文件可以在數據庫裡自動生成相應的數據表。
- `__init__.py` : `<app_name>` 文件夾的初始化文件。
- `admin.py` : 用於設置當前 APP 的後台管理功能。
- `models.py` : 定義數據庫的映射類，每個類可以關聯一張數據表，實現數據持久化，即 `MTV` 裡面的模型 (Model)。
- `test.py` : 自動化測試模塊，用於實現單元測試。
- `views.py` : 視圖文件，用於處理功能的業務邏輯，即 `MTV` 裡面的視圖 ( Views ) 。

### 啟動
```sh
python manage.py runserver <port-number>
```
### Django 操作指令
| 指令                        | 說明                                                                                                     |
| ------------------------- | ------------------------------------------------------------------------------------------------------ |
| changepassword            | 修改內置用戶表的用戶密碼                                                                                           |
| createsuperuser           | 為內置用戶表創建超級管理員帳號                                                                                        |
| remove_stale_contenttypes | 刪除數據庫中已經不使用的數據表                                                                                        |
| check                     | 檢測整個項目是否存在異常                                                                                           |
| compilemssages            | 編譯語言文件，用於項目的區域語言設置                                                                                     |
| createcachetable          | 創建緩存數據表，為內置的緩存機制提供存儲功能                                                                                 |
| dbshell                   | 進入Django配置的數據庫，可以執行數據庫的SQL語句                                                                           |
| diffsettings              | 顯示當前 settings.py 的配置訊息與默認配置差異                                                                          |
| dumpdata                  | 導出數據表的數據並以json儲存，如`python manage.py dump data index > data.json`，以 index 模型所對應的數據倒出，並保存 data.json 文件中。 |
| flush                     | 清除資料庫資料，僅保留表結構                                                                                         |
| inspectdb                 | 獲取項目所有模型的定義過程                                                                                          |
| makemessages              | 創建語言文件，用於項目的區域語言設置                                                                                     |
| loaddata                  | 將數據文件導入數據表，如`python manage.py loaddata data.json`                                                      |
| makemigrations            | 從模型對象創建數據遷移文件並保存在APP的migrations文件夾中。                                                                   |
| migrate                   | 根據遷移文件內容，在數據庫裡生成相應的數據表                                                                                 |
| optimizemigration         | 優化遷移操作並覆蓋現有得遷移文件                                                                                       |
| sendtestmail              | 向指定的收件人發送測試的電子郵件                                                                                       |
| shell                     | 進入Django的Shell模式，用於調用項目功能                                                                              |
| showmigrations            | 查看當前項目的所有遷移文件                                                                                          |
| sqlflush                  | 查看清空數據庫的SQL語句腳本                                                                                        |
| sqlmigrate                | 根據遷移文件內容輸出相應的SQL語句                                                                                     |
| sqlsequencereset          | 重置數據表梯增字段的索引值                                                                                          |
| squashmigrations          | 對遷移文件進行壓縮處理                                                                                            |
| startapp                  | 創建項目應用app                                                                                              |
| startproject              | 創建新的Django項目                                                                                           |
| test                      | 運行app裡面的測試程序                                                                                           |
| testserver                | 新建測試數據庫，並使用該數據庫運行項目                                                                                    |
| clearsessions             | 清除會話 ( Session ) 數據                                                                                    |
| collectstatic             | 收集所有的靜態文件                                                                                              |
| findstatic                | 查找靜態文件的路徑信息                                                                                            |
| runserver                 | 在本地啟動Django項目                                                                                          |

