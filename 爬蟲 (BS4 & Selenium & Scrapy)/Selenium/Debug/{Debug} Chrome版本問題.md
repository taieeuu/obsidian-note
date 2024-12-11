## 錯誤訊息
```shell
selenium.common.exceptions.SessionNotCreatedException: Message: session not created: This version of ChromeDriver only supports Chrome version 114
Current browser version is 120.0.6099.131 with binary path C:\Program Files\Google\Chrome\Application\chrome.exe
```

## 解決方案
1. 查看Chrome版本號
	![[Pasted image 20240107131958.png]]
2. 在chrome中，搜尋ChromeDriver + 屬於你的版本號，且下載chromedriver，並添加到電腦環境變數中。
	![[Pasted image 20240107133303.png]]

3. 修改.py
```python
## define the website to scrape and path where the chromediver is located or u can choose to move chromedriver in the project.
website = 'https://www.adamchoi.co.uk/overs/detailed'
path = 'C:\Program Files\Google\Chrome\Application\chromedriver-win64\chromedriver.exe'

options = webdriver.ChromeOptions()
service = Service(executable_path=path)  # selenium 4
driver = webdriver.Chrome(service=service, options=options)  # define 'driver' variable
```