---
Date: 2024-02-07
tags:
  - 資安
  - 資安工具
---
## 基本主機掃描
```
nmap  target_ip
```

輸出結果

```
Starting Nmap 6.40 ( http://nmap.org ) at 2014-10-02 08:09 CST
Nmap scan report for www.hinet.net (202.39.253.11)
Host is up (0.0034s latency).
rDNS record for 202.39.253.11: 202-39-253-11.HINET-IP.hinet.net
Not shown: 998 filtered ports
PORT    STATE  SERVICE
80/tcp  open   http
113/tcp closed ident
Nmap done: 1 IP address (1 host up) scanned in 6.05 seconds
```

**說明**  
Host is up : 列出目標裝置的狀態為up(表示這台處於開機)。  
Not show : 進行檢查1000通訊埠中，有998個關閉。  
Port : 通訊埠 ， State : 狀態 ， Service : 提供的服務
## 掃描多台主機
```
nmap www.hinet.net tw.yahoo.com www.google.com.tw ...
```

or

```
nmap 192.168.0.*
```

or

```
nmap 192.168.0.0/24
```
## 以檔案列表指定主機
文檔為IP列表

```
nmap -iL hostlist.txt
```
## 排除指定的主機
參數 –exclude 除外

```
nmap 192.168.0.* --exclude 192.168.0.100
```
## 偵測作業系統版本
偵測主機的作業系統與各種服務的版本

```
nmap -A scanme.nmap.org
```

只有偵測作業系統資訊 參數 -O

```
nmap -O scanme.nmap.org
```
## 測試主機是否有防火牆
```
nmap -PN scanme.nmap.org
```
## 掃描有防火牆的主機

```
nmap -參數 target_ip,...
```

## 偵測有開機的主機
掃描整個網路，偵測所有有開機的主機（ping scan

```
nmap -sP 140.115.35.0/24
```
## 快速掃描
```
nmap -F www.hinet.net
```
## 指定掃描的連接埠
掃描連接埠80:

```
nmap -p 80 192.168.1.1
```

指定 TCP 連接埠 80

```
nmap -p T:80 192.168.1.1
```

指定 UDP 連接埠 53：

```
nmap -p U:53 192.168.1.1
```

掃描兩個連接埠：

```
nmap -p 80,443 192.168.1.1
```

指定連接埠範圍：

```
nmap -p 80-200 192.168.1.1
```

## 查詢主機名稱
只查詢網域中所有的主機名稱，不做任何主機與連接埠的偵測：

```
nmap -sL 192.168.1.0/24
```

---

# 何種方式進行裝置發現

## ARP

```
nmap -sn 192.168.157.0/24
```

**補充**  
ARP協定主要用來解決邏輯位址分析和物理位址的轉換關係。  
網路通訊主要使用到兩個位址-物理(MAC)and邏輯，同一網段使用物理，不同網段則用邏輯

同一網段中，所有的裝置都會連接到交換機，交換機內容會儲存一張包含每個連結裝置的物理位址表-ARP表，如果發送封包時，不清楚目標物理位址是未知的，就需要這個額外的通訊來解析這個位址。  
eg.  
192.168.0.1想連接到192.168.0.2進行通訊，但不知道192.168.0.2的物理位址(MAC)，則須ARP。

## ICMP

```
nmap -PE 目標裝置IP位址
```

**補充**  
ICMP分成兩類差錯和查詢，Nmap主要是使用查詢的封包進行發現。  
下列三種:  
回送請求和回答  
時間戳記請求和回答  
位址隱藏請求和回答

## TCP

**SYN掃描**

```
nmap -PS [通訊埠1,通訊埠2...][目標ip]
```

**ACK 掃描**

```
nmap -PA [通訊埠1,通訊埠2...][目標ip]
```

**補充**  
TCP主要過程是三次驗證  
第一次:用戶發送SYN(Seq=x)資料封包服務端，進入SYN_SEND狀態。  
第二次:收到後，回應一個SYN(Seq=y)+ACK(ack+=1)封包，進入syn_recv狀態。  
第三次:收到後，回應ACK(Y+=1)封包，進入ESTABLSHED狀態

## UDP

```
nmap -PU 目標IP
```

## 選項說明

- -sT TCP connect()掃描，這種方式會在目標主機的日誌中記錄大批連接請求和錯誤信息。
- -sS TCP SYN（半公開）掃描，因為它沒有完成一個完整的 TCP 連接三次交握過程。  
    此方法向目標 Port 發送一個 SYN 封包，如果目標 Port 回應 SYN / ACK 封包則表示該 Port 處於打開狀態；若回應的是 RST / ACK 封包則表示該 Port 為關閉狀態。因為沒有回傳ACK封包導致三項交握未完成，故比較不會在目標系統留下掃描痕跡。
- -sF -sN Stealth FIN、Xmas Tree或 NULL秘密掃描(Stealth Scanning)  
    一些防火牆及 Packet 過濾裝置會在重要連接 Port 守護，半公開的 SYN 封包掃描會在此時會被截獲因此要有更秘密的進行掃描；而 FIN、Xmas、NULL 掃描方法則是關閉的連接埠會對你送出的探測資訊包返回一個 RST，而打開的連接埠則對其忽略不理可參考 [RFC793]，其中 FIN 掃瞄使用空的 FIN 資訊包作為探針、Xmas tree 使用 FIN、URG、PUSH 標記、Null 掃描則不用任何標記，但微軟不支援此一標準，所以 -sF,-sX,-sN 的掃瞄顯示所有連接埠都是關閉的但一個 SYN(-sS) 掃瞄卻顯示有打開連接埠，那你就能大致推斷它是 WINDOWS 平台。
- -sP ping ICMP 掃描 (Ping Sweeping)  
    若僅想了解網路上有哪些主機是開放的，可對指定的IP地址送出一個 ICMP 的 Echo Request 封包來偵測。但有些站台會把 ICMP 的 Echo Request 封包給關掉 ([例如Microsoft.com](http://xn--microsoft-pg9n309f.com/))。在這種情況下可利用發送 TCP Ping 送一個 ACK 到目標網路上的每個主機。網路上的主機如果在線，則會返回一個 TCP RST 響應。使用帶有 Ping 掃描的 TCP Ping 選項，也就是 -PT 選項可以對網絡上指定 Port 進行掃描，它將可能通過目標邊界路由器甚至是防火牆。注意，被探測的主機上的目標 Port 無須打開，關鍵取決於是否在網路上。
- -sU UDP掃描  
    原理為送出零位元組的 UDP 封包到目標主機的各連接埠，如果我們收到一個 ICMP port unreachable 無法到達的回應則可確定連接埠是關閉的，否則我們可認為該埠是打開的。
- -sA (serviceAdvanced)這項高級的掃描方法通常用來穿過防火牆的規則集
- -sV (serviceVersion)探測端口服務版本
- -Pn (PingNo)掃描之前不需要用ping命令，有些防火牆禁止ping命令。可以使用此選項進行掃描
- -v (view) 顯示掃描過程，會有很詳細的說明執行到哪個步驟
- -h (help) 幫助選項，是最清楚的幫助文檔
- -p (port) 指定端口，如“1-65535、1433、135、22、80”等
- -O 掃描使用的作業系統  
    通常一個入侵者可能對某個作業系統的漏洞很熟悉，能藉由漏洞很輕易地進入此作業系統的機器。一個常見的選項是 TCP / IP 上的指紋，帶有 -O 選項決定遠端作業系統的類型。這可以和一個 Port 掃描結合使用，但不能和 Ping 掃描結合使用。 ex. nmap -sS -O [www.yourserver.com](http://www.yourserver.com/)
- -A (All) 全面系統檢測、啟用腳本檢測、掃描等
- -oN/-oX/-oG (outputNormal、xml、grepable)將報告寫入文件，分別是正常、XML、grepable 三種格式
- -T4 針對TCP端口禁止動態掃描延遲超過10ms
- -iL 讀取主機列表，例如，“-iL C:\ip.txt”