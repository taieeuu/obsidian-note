---
Date: 2024-05-20
---
這次因主管需求使用scrapy這個框架進行爬蟲，應該算第一次真正實作scrapy這個架構的爬蟲。爬蟲主要目的是公司想要提升自己在遊覽器搜索上，能夠更容易被大眾所查詢到，也就是搜索引擎優化 SEO，以至於需要大量文章的需求，文章的需求會使用 AI 來做產出，但前置的資料會使用爬蟲來進行抓取。

首先來簡單講一下基本的環境，與架構。

- python: 3.8.19
- scrapy: 2.11.2

### 架構
這是官網架構圖。
![[Python Scrapy 1.png]]
>[!info]
> - Spiders : 是 Scrapy 框架中的爬蟲組件。每個 Spider 是一個 Python 類別，負責定義如何從特定網站或一組網站中抓取信息。這些信息通常包括從網頁提取數據的規則（使用 XPath 或 CSS 表達式）。
> - Spider Middlewares : 是介於 Scrapy 的 Spider 和 Engine 之間的組件，負責處理 Spider 輸出的響應以及傳遞到 Spider 的請求。它們是數據處理流程中的重要部分，提供了一種機制來擴展 Scrapy 功能，通過添加自定義的中間件，你可以在請求的發送和響應的處理過程中執行自定義的操作，像是 處理請求、處理響應、處理異常...等。
> - Scrapy Engine : 是整個框架的核心，負責控制數據抓取的主要流程。它作為其他組件（如 Spiders、Scheduler、Downloader 和 Item Pipeline）之間的中樞，協調這些組件的互動。
> - Scheduler : 負責接收來自 Spider 的請求並按照某種策略決定其處理的順序。此外，它還負責確保請求不被重複處理，通過去重機制來避免爬取相同的資源多次，而通常是在引擎啟動時自動創建和配置的，並且不需要用戶直接操作。然而，了解其工作原理有助於進行更高級的自定義和優化。
> - Item Pipeline : 是專門用來處理由 Spiders 提取並提交的數據項（items）的組件。當 Spider 提取了數據後，這些數據將會通過一系列的處理步驟，這些步驟在 Item Pipeline 中定義。Pipeline 使得數據的處理過程變得模組化和可定制，方便用戶根據自己的需要處理數據，也就是將數據儲存為json、DB、CSV...等方式。


### Let's Start

環境安裝完且進入環境後，我們可以使用以下的命令創建scrapy project
```cmd
scrapy startproject news_spider
```
然後我們將要爬取的網站以及邏輯處理寫在spiders的資料夾底下，以下為範例代碼。
```python
class IC_TPEX_2(scrapy.Spider):
    name = 'ic_tpex_2'
    start_urls = ['https://ic.tpex.org.tw/introduce.php?ic=D000']
    
    def parse(self, response: Response) :
        # IP/IC設計
        yield from self.extract_companies(response, row_line = ['2.4', '1.2', '1', '1.6', '1.8','0'], rows = response.xpath('//*[@id="companyList_DC00"]/div/table//tr'), table_name = 'IP/IC設計', industry_phase = '上游')
		# IC通路
        yield from self.extract_companies(response, row_line = ['4.2', '4.4', '1.4', '1.4', '0'], rows = response.xpath('//*[@id="companyList_DB00"]/div/table//tr'), table_name='IC通路', industry_phase = '下游')

    def extract_companies(self, response, row_line, rows, table_name, industry_phase):
        i, count, last_category = 0, -1, ''
        for row in rows:
            category=''
            if row_line[i] == '0' and count<=0:
                break

            if count<=0:
                category = row.xpath(f'./td[@class="company" and @rowspan="{row_line[i]}"]/b/text()').get()
                match = re.search(r'\((\d+)家\)', category)
                if match:
                    count = int(match.group(1))
            if category:
                # self.logger.info(f'Category found: {category}')
                last_category = category
                i += 1
            if last_category:
                for company in row.xpath('./td/a[@class="company-text-over"]'):
                    count-=1
                    item = TestItem()
                    name = company.xpath('./text()').get()
                    link = company.xpath('./@href').get()
                    item['industry_phase'] = industry_phase
                    item['table_name'] = table_name
                    item['category'] = last_category
                    item['company_name'] = name
                    item['company_url'] = response.urljoin(link)
                    yield item
```
>[!info]
>- 我們會先創建一個class 然後繼承 scrapy.Spider ，name參數為之後要爬取時，所要執行哪個class的名稱，start_urls為實際要爬取的網站。
>- 函數 parse 為主要要爬取網站資料的邏輯處理。

當我們爬取完數據後，會經過配置在 settings.py 中啟用的 `item pipelines`，如果說你的爬蟲有較複雜，每個 spider 會使用到不同的 pipelines 時，我們可以設定動態的 pipelines，而不是在settings.py中設置`ITEM_PIPELINES`，如下程式碼範例，100為優先順序。
```python
class MySpider(scrapy.Spider):
    name = 'my_spider'
    custom_settings = {
        'ITEM_PIPELINES': {
            'myproject.pipelines.SpecialPipeline': 100,
        }
    }
```
因為如果你在settings中設置了，你會發現只要你執行都會經過該 pipeline ，像是如果你運行下面指令。
```cmd
scrapy crawl my_spider -o my_spider.json
```
你會發現他也會執行你在settings.py中設定啟用的`ITEM_PIPELINES`，並指定輸出為 JSON 文件時，Scrapy 的 Feed Exporter 負責處理這部分的數據輸出，如果你想要制定其他存儲到文件或任何其他支持的輸出介質中的話，可以在settings.py中設置 Feed Exporter。

而這邊我 pipelines 寫的是存入 sqlite 中，所以這邊也會執行一次，也就是你會有兩個檔案 `.db`、`.json`，

這是我的pipelines.py 
```python
class PocketSpeechPipeline:
    def open_spider(self, spider):
        self.connection = sqlite3.connect('IC_TPEX.db')
        self.cursor = self.connection.cursor()
    def close_spider(self, spider):
        self.connection.close()
    def process_item(self, item, spider):
        self.cursor.execute(f"""
            CREATE TABLE IF NOT EXISTS {item.__class__.__name__} (
                industry_phase TEXT,
                table_name TEXT,
                category TEXT,
                company_name TEXT,
                company_url TEXT
            )
        """)
        self.connection.commit()
        self.cursor.execute(f"""
            INSERT INTO {item.__class__.__name__} (industry_phase, table_name, category, company_name, company_url) VALUES (?,?,?,?,?)
        """, (item['industry_phase'], item['table_name'], item['category'], item['company_name'], item['company_url']))
        self.connection.commit()
        return item
```

這邊的item參數是從yield那邊傳過來的，而在 IC_TPEX_2 class 中的 item 是在 items.py中所定義的，如下程式碼。
```python
class TestItem(scrapy.Item):
    industry_phase = scrapy.Field()
    table_name = scrapy.Field()
    category = scrapy.Field()
    company_name = scrapy.Field()
    company_url = scrapy.Field()
```
原本其實寫了很多個 class 爬蟲，寫完後發現基本的架構都是一樣的，只有分為兩種情況，所以就把總共12個 class 重構成了兩個。
再來因為我想要一次就可以執行我整個爬蟲，所以我寫了一個run_spider.py，這樣讓我可以更方便。
```python
import scrapy
from scrapy.crawler import CrawlerProcess
from scrapy.utils.project import get_project_settings
from pocket_speech.spiders.ic_tpex_org import *

process = CrawlerProcess(get_project_settings())
process.crawl(IC_TPEX_1)
process.crawl(IC_TPEX_2)
process.start()
```
現在目前還沒有使用到 middlewares ，等之後有使用到在做補充!!!!