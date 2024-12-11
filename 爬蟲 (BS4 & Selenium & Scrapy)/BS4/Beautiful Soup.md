* 解析靜態網頁
## Beautiful Soup Libraries
```
requests

bs4

html.parser
```
## 抓取數據
```python
from bs4 import BeautifulSoup
import requests

#################################################

# Extracting a movie transcript

#################################################

# How To Get The HTML
website = 'https://subslikescript.com/movie/Titanic-120338'

result = requests.get(website)

content = result.text
```
## 尋找元素
#### HTML Code example
![[Pasted image 20240106225205.png]]
```python
soup = BeautifulSoup(content, 'lxml')

# Locate the box that contains title and transcript
box = soup.find('article', class_='main-article')

# Locate title and transcript
title = box.find('h1').get_text()
```
#### 獲取多個相同標籤
```python
soup.find_all("h2")
```

## 實作
* 目標網站: https://subslikescript.com/movie/Titanic-120338
```python
from bs4 import BeautifulSoup

import requests

#################################################

# Extracting a movie transcript

#################################################

# How To Get The HTML

website = 'https://subslikescript.com/movie/Titanic-120338'

result = requests.get(website)

content = result.text

soup = BeautifulSoup(content, 'lxml')

# print(soup.prettify())  # prints the HTML of the website

# Locate the box that contains title and transcript
box = soup.find('article', class_='main-article')

# Locate title and transcript
title = box.find('h1').get_text()

transcript = box.find('div', class_='full-script').get_text(strip=True, separator=' ')

  

# Exporting data in a text file with the "title" name
with open(f'{title}.txt', 'w', encoding='UTF-8') as file:

    file.write(transcript, )
```