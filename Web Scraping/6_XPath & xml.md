## XPath 
BeautifulSoup不支援XPath，但Selenium和Scrapy支援

| 選取描述說明 | CSS選擇器 | XPath表達式 | 
| :---: | :---: | :---: | 
|tag搜尋|a|//a|
|class搜尋|div.home|//div[@class='home']|
|條件搜尋tag+id|span#test|//span[@id='test']|
|條件搜尋tag+屬性|div[class*='test']|//div[contains(@class,'test')]|
|條件搜尋(tag='div'且子標籤=p或a)|div p,div a|//div[p\|a]|


<br></br>
## xml解析
讀取xml檔案
```python
import xml.etree.ElementTree as ET

tree=ET.parse('<file.xml>')     # 讀取xml檔案
root=tree.getroot()             # 取得根結點
```
查看樹狀結構
```python
ET.dump(tree)
```


root屬性
```python
root[0]                 # root為容器，使用index搜尋

root.tag                # 標籤
root.text               # 文字內容

root.attrib             # 屬性 多為dict型態
root.attrib[<key>]      # 屬性 values
```


root.append() 新增節點  
```python
import copy

root.append(<tag>)          # 新增新檔案

# copy_data=copy.deepcopy(root[0])
# copy_data.attrib[<key>]=values
# root.append(copy_data)
# 若是使用舊檔案新增需用deepcopy，以免舊檔案會一併被修改
```

修改值 
```python
root.find(<tag>).text=<text>                # 可以指定tag，直接進行修改
root.find(<tag>).attrib[<key>]=<values>     # attrib內容一定要是string，不可是int
```

root.remove() 刪除值  
iter() 沒辦法進行刪除，雖然可以找到值，但沒有辦法取得階層關係，只能用find()
```python
root.remove(<tag>)      # 刪除標籤
```

find()  搜尋值  
只能依照樹狀結構一層一層往下找，無法直接找子孫標籤
```python
root.find(<tag>)        # 標籤搜尋
```

findall()  搜尋值  
同find()，無法直接找子孫標籤
```python
root.findall(<tag>)     # 標籤搜尋，回傳list
```

iter()  搜尋值  
```python
root.iter(<tag>)        # 可以直接搜尋，不用按照順序往下找
```








