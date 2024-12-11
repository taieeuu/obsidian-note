---
Date: 2024-10-31
---
 前幾天看到 YT 上，有人介紹了 **pdb** ，少用 print() 來進行debugger，於是我就來學了一下。

一般我們在除錯 (Debug) 時，會使用 `print` 方式，是最簡單、直接的方法。另一種更強大的方式是使用 **調試器（Debugger）**，而調試器不僅可以查看變數的值，還能逐步執行程式、設置斷點、查看呼叫堆疊等，是處理複雜錯誤時的理想工具，此篇章介紹 python 內建 `debugger pdb`。
### 使用 `pdb` 調試器進行除錯
`pdb` 調試器能讓你在程式的某一行程式碼上暫停，並一步一步地查看程式的執行情況。以下是如何使用 `pdb` 來進行除錯的介紹
#### **啟動 `pdb` 調試器**
##### 在希望暫停的地方加入以下程式碼 :
```python
import pdb; pdb.set_trace()

or 

def conditional_debug(debug=False): 
	if debug: 
		import pdb; pdb.set_trace()
```

當程式執行到這行程式碼時會自動暫停，進入 `pdb` 調試模式。或是你也可以區分環境逕行添加。
##### 直接使用命令行進入 `pdb`
```python
python -m pdb <script_name>
```

然後再用命令方式設定斷點
```python

```
#### pdb 常用命令
- `l` 或 `list`: 列出當前程式碼附近的數行程式碼，方便查看上下文。默認當前行數上下 11 行。再輸入一次，會打印再往下 11 行。`1 .`: 會回到當前行數
- `ll` 或是 －`longlist`: 列出當前這個函數的全部代碼。
- `n` 或 `next`: 執行下一行，不進入函式內部。
- `s` 或 `step`: 進行一步執行，會進入函式內部。
- `c` 或 `continue`: 繼續執行程式直到下一個斷點或結束。
- `p [變數名稱]`: 查看變數的當前值，例如 `p result`。
- `q` 或 `quit`: 結束 `pdb` 調試模式並退出程式。
- `w` 或 `where`: 可以查看目前的調用棧，讓你了解程式是如何執行到當前位置的，python 的調用棧是由上到下，也就是最先被執行的函數會在最上面。
- `u`或 `up`: 將當前幀往上一層，即移到上一個調用的框架。
- `d` 或 `down`: 將當前幀往下一層，即回到更深一層的框架。
- `retval`: 獲取返回值
- `until`: 運行直到你的行數比我現在行數大為止，用來快速結束循環。
- `r` 或 `return`: 運行到他返回為止，他會停在返回函數之前。
- `b` 或 `break`: 設置斷點位置(可以是行數也可以是function_name或是斷點行數,條件)，如果什麼都不加則列出所有斷點。
- `clear`: 清除斷點，可添加斷點編號，如果不加則清除全部。

在查看調用棧時，左邊會有一個 `>` 箭頭，他代表當前所在的 `frame`，當我們打印變量時，他會從當前幀的局部變量去找。

```python
(Pdb) where
  /Users/taieeuu/.pyenv/versions/3.11.10/lib/python3.11/bdb.py(600)run()
-> exec(cmd, globals, locals)
  <string>(1)<module>()
> /Users/taieeuu/Project/Leetcode/5.py(1)<module>()
-> class Solution:
(Pdb) 
```