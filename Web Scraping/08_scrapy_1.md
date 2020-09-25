## 1. 套件安裝
從channel conda-forge 安裝套件
```shell
conda install -c conda-forge scrapy
```


## 2. Shell 操作
啟動、離開scrapy
```shell
$ scrapy shell
$ quit
```

建立專案目錄
```shell
$ scrapy startproject <dirname>
```

取網頁資訊
```shell
$ fetch(<url>)              # DEBUG: Crawled (200) 表示請求成功
$ view(response)            # 瀏覽器開啟網頁
$ repsonse.text             # 檢視HTML標籤
```
定位
```shell
$ response.css("<selector>")    # 使用css selector
$ response.xpath("<xpath>")     # 使用xpath
```

取標籤文字(css)
```shell
$ response.css("<selector>::text")                  # 回傳類list，含有標籤文字內容
$ response.css("<selector>::text").get()            # 回傳str，取第一個
$ response.css("<selector>::text").getall()         # 回傳list，取全部
$ response.css("<selector>::text").extract()        # 回傳str，取第一個
$ response.css("<selector>::text").extract_first()  # 回傳list，取全部
```
取標籤屬性(css)
```shell
$ response.css("<selector>::attr(<attrib>)").get()
# response.css("#main-container::attr(href)").get() 舉例
```

取標籤文字(xpath)
```shell
$ response.xpath("<xpath>/text()").get()     # 加 /text()
```
取標籤屬性(xpath)
```shell
$ response.xpath("<xpath>/@<attrib>").get()  # 加 /@<attrib>
```

取標籤屬性(re)
```shell
$ response.re("Name:<re_string>")              # 只有Name:後的文字內容
```

