## 建立scrapy專案
### 1.新增專案資料夾
```shell
$ scrapy startproject <project_name>        
``` 

|目錄檔案|作用|   
| - | - | 
| items.py | 定義擷取資料的項目 (需要擷取的欄位資料) |
| settings.py | scrapy設定檔，可設定專案的延遲時間和輸出方式 |
| pipelines.py | 客製化資料處理|
| spider 目錄 | 爬蟲程式所在位置 |  
<br>  

### 2.在專案資料夾新增爬蟲程式
```shell
$ scrapy genspider <spider_name> <domain>   
```
### 3.撰寫爬蟲程式
```python
import scrapy

class Spider(scrapy.Spider):
    name = '<spider_name>'
    allowed_domains = ['<domain>']
    start_urls = ['<url>']
    
    def parse(self,response):
```
|類別|說明|
| - | - |
| name屬性|爬蟲程式名稱，執行時會用到|
| allowed_domains屬性|定義允許擷取的網域清單，若無定義表示任何網域都可擷取|
| start_urls屬性|開始擷取的網址清單|
| parse()函數|擷取程式碼|
<br>  

範例:Ptt NBA
```python
import scrapy


class PttnbaSpider(scrapy.Spider):
    name = 'pttnba'
    allowed_domains = ['ptt.cc']
    start_urls = ['https://www.ptt.cc/bbs/NBA/index.html']

    def parse(self, response):
        titles=response.css("div.r-ent > div.title > a::text").extract()
        votes=response.xpath("//div[@class='nrec']/span/text()").extract()
        authers=response.xpath("//div[@class='meta']/div[1]/text()").extract()
        for item in zip(titles,votes,authers):  # 用zip打包成tuple，再用dict取每一篇發文
            scraped_info={
                "titles":item[0],
                "votes":item[1],
                "authors":item[2]
            }
            yield scraped_info
```

<br>  

### 4.執行爬蟲程式
執行
```shell
$ scrapy crawl <spider_name>
```
下載檔案
```shell
$ scrapy crawl <spider_name> -o <file_name> # -o選項指定輸出格式的檔案

$ scrapy crawl pttnba -o pttnba.json        # NBA範例
```
更改編碼，開啟setting.py
```python
FEED_EXPORT_ENCODING='utf-8'
```

### 5.處理下一頁資料
用於建置絕對url，當傳入的參數為一個相對url時，會根據response.url組合成對應的url
```python
response.urljoin(<相對url>)
```

scrapy.Request(<url>,callback=self.parse)   連結url，並且呼叫parse函數
```python
yield scrapy.Request(next_page,callback=self.parse)     # callback預設self.parse
```

範例:quotes
```python
import scrapy

class QuotesSpider(scrapy.Spider):
    name="quotes"
    allowed_domains=["quotes.toscrape.com"]
    start_urls=["https://quotes.toscrape.com/"]
    def parse(self,response):
        for quote in response.css("div.quote"):
            yield{
                "text":quote.css("span.text::text").extract_first(),
                "author":quote.css("small.author::text").extract_first(),
                "tag":quote.css("div.tags a.tag::text").extract()
            }
        next_page=response.css("li.next a::attr(href)").extract_first()
        if next_page is not None:
            next_page=response.urljoin(next_page)
            yield scrapy.Request(next_page,callback=self.parse)
            
```

### 6.抓取下一頁資訊
```python
response.follow()       # 支援相對url，相較scrapy.Request更簡潔
```
更改程式碼
```python
import scrapy

class QuotesSpider(scrapy.Spider):
    name="quotes2"
    allowed_domains=["quotes.toscrape.com"]
    start_urls=["https://quotes.toscrape.com/"]
    def parse(self,response):
        for quote in response.css("div.quote"):
            scraped_quote={
                "text":quote.css("span.text::text").extract_first(),
                "author":quote.css("small.author::text").extract_first(),
                "tag":quote.css("div.tags a.tag::text").extract(),
                "birthday":None
            }
            authorHref=quote.css(".author+a::attr(href)").extract_first()
            author_page=response.urljoin(authorHref)
            yield scrapy.Request(author_page,meta={"item":scraped_quote},callback=self.parse_author)
        
        # 下一頁
        next_page=response.css("li.next a::attr(href)").extract_first()
        if next_page is not None:
            yield response.follow(next_page,callback=self.parse)
    
    def parse_author(self,response):
        item=response.meta["item"]
        b=response.css(".author-born-date::text").extract_first().strip()
        item["birthday"]=b
        return item


```









