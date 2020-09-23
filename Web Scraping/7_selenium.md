## 安裝套件
```shell
pip install selenium 
```
## 開啟網頁  
 
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
## selenium 定位  
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

## 例外物件
```python
ElementNotSelectableException       # 選取的是不被允許的元素
ElementNotVisibleException          # 元素存在，但不可見
ErrorResponseException              # 伺服器端錯誤
NoSuchAttributeException            # 選取元素的指定的屬性不存在
NoSuchElementException              # 選取的元素不存在
TimeoutException                    # 超過時間限制
```

## 動作鏈
滑鼠、鍵盤操作
```python
link=driver.find_element_by_xpath(xpath)

link.click()                        # 點左鍵
context_click()                     # 點右鍵
double_click()                      # 雙擊左鍵
link.click_and_hold()               # 按住左鍵，不鬆開
drag_and_drop(<source>,<target>)    # 滑鼠拖曳至某個元素鬆開
move_to_element()                   # 滑鼠移動至元素
key_down()                          # 按下鍵盤某一鍵
key_up()                            # 放開鍵盤某一鍵
perform()                           # 執行所有儲存動作
send_keys()                         # 送出按鍵至元素
release()                           # 在元素上鬆開滑鼠按鍵
```

搜尋欄位
```python
search_bar=driver.find_element_by_xpath(xpath)

search_bar.send_keys(<string>+'\n')             # \n為enter
search_bar.send_keys(Keys.ENTER)
```
send_keys 常見按鍵
```python
keys.ENTER
keys.SPACE
keys.SHIFT
keys.LEFT_SHIFT
keys.CONTROL
keys.ALT
```

下拉式選單 範例
```python
from selenium import webdriver
from selenium.webdriver.common.action_chains import ActionChains

# 開啟網頁
driver=webdriver.Chrome(r'.\chromedriver')
driver.implicitly_wait(10)
driver.get('http://www.python.org/')

# 定位
menu=driver.find_element_by_css_selector('#about')
item=driver.find_element_by_css_selector('#about ul li.tier-2.element-1 a')

# 設定ActionChains (四行可以合併)
actions=ActionChains(driver)        # ActionChains(driver)
actions.move_to_element(menu)       # 滑鼠移動至menu元素
actions.click(item)                 # 點選item
actions.perform()                   # 執行以上動作

time.sleep(5)
driver.quit()
```











