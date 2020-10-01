## 爬取方式
* 爬取數頁內容 >>> requests, BeautifulSoup
* 爬取動態javascript >>> selenium
* 爬取整個網站大量資料 >>> scrapy框架  

## 定位網頁資料方式
* CSS selector  
* XPath  
* Regular Expression 

## 爬取後資料型態
* csv 
* json  
* xml

## 常見問題-爬取不到
更改headers
```python
r = requests.get(url, headers=headers)
```

增加延遲時間
```python
time.sleep(n)
```
## 常見問題-網站分級(ptt)
```python
r = requests.get(url, cookies={"over18": "1"})
```









