
目標網站: https://www.audible.com/search

**注意:** 此專案使用selenium == 3.141.0


## 函數
### 瀏覽器不顯示模式
```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
  
# Headless mode
options = Options()  # Initialize an instance of the Options class

path = 'C:\Program Files\Google\Chrome\Application\chromedriver-win64\chromedriver.exe'
driver = webdriver.Chrome(path, options=options)
```


### implicit wait 隱式等待
等待2毫秒，確保數據加載完成
```python
time.sleep(2)
```
### Explicit Wait 顯式等待
等待直到這種情況發生，最多等待10秒
```python
WebDriverWait(driver, 10).until(...)
```