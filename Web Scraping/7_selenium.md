## 安裝套件
```shell
pip install selenium 
```
### 開啟網頁  
 
```python
from selenium import webdriver
import time

driver=webdriver.Chrome(r'./chromedriver')   # chromedriver放在目錄資料夾
# "./" 代表目前所在的目錄  
# "../"代表上一层目錄  
# "/"  代表根目錄 
driver.implicitly_wait(10)                   # 等待最多10秒，開啟瀏覽器即停止等待
driver.get(<url>)                            # 連結至url

print(driver.page_source)                    # 網頁原始內容，可丟進BeautifulSoup

driver.quit()
```
selenium 定位  
find_element_by_<> 和find_elements_by_<> 兩種函數
```python
driver.find_element_by_id()                 # 以id
driver.find_element_by_name()               # 以name屬性
driver.find_element_by_xpath()              # 以XPath屬性
driver.find_element_by_tag_name()           # 以標籤名稱
driver.find_element_by_class_name()         # 以class屬性
driver.find_element_by_css_selector()       # 以CSS選擇器
driver.find_element_by_link_text()          # 以超連結文字
driver.find_element_by_partial_link_text()  # 以部分超連結文字
```

