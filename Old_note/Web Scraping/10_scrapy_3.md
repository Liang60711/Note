# 使用Item
開啟items.py檔案
```python
import scrapy

class NBAItem(scrapy.Item):         # 1.定義函數名稱
    title=scrapy.Field()            # 2.定義scrapy.Field()物件
    vote=scrapy.Field()
    author=scrapy.Field()
```
開啟Spider程式
```python
import scrapy
from <project名稱>.items import NBAItem     # 3.從檔案import 剛定義的函數
class PttnbaCrawlerSpider(scrapy.Spider):
    name = 'pttnba_crawler'
    allowed_domains = ['ptt.cc']
    start_urls = ['https://www.ptt.cc/bbs/NBA/index.html']

    def parse(self, response):
        item=NBAItem()                      # 4.用函數建立item物件
        for sel in response.css(".r-ent"):
            item["title"]=sel.css("div.title a::text").get()
            item["vote"]=sel.css("div.nrec span::text").get()
            item["author"]=sel.css("div.meta div.author::text").get()
            yield item
```

# 使用Item Pipeline
Item Pipeline：spider每抓取到一個(item)項目後，便會送到Item Pipeline，經過多個元件依序串起來成為一個資料處理的管線。  

Item Pipeline應用：
* 清洗資料
* 驗證資料
* 過濾重複資料
* 資料存入資料庫  

開啟pipelines.py
```python
from scrapy.exceptions import DropItem      # 依照情況import 

class PttnbaPipeline(object):
    def process_item(self, item, spider):
        if item["vote"]:                    # 1.撰寫條件
            if item["vote"]=="爆":
                item["vote"]=500
            else:
                item["vote"]=int(item["vote"])
            return item
        else:
            raise DropItem("沒有推文數:%s"%item)
```
開啟setting.py
```python
# dict型態，
# key=<project名稱>.pipelines.<pipeline的module名稱>
# value=<0~1000 執行順序，低的先執行>

# 2.把註解打開
ITEM_PIPELINES = {
   'pttnba.pipelines.PttnbaPipeline': 300,
}
```

# 支援中文字
開啟setting.py，新增一行
```python
FEED_EXPORT_ENCODING="utf-8"
```

# 設定輸出
開啟setting.py，可輸出json, csv, xml，完成後即不需打-o pttnba.json
```python
FEED_FORMAT = "JSON"
FEED_URI = "pttnba.json"
FEED_EXPORT_ENCODING = "utf-8"
```