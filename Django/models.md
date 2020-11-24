# Models
Model 是儲存在 Application 的資料庫。
* 基本上一個 Model 對應一個資料表
* Model 內的每一個屬性(attribute)是資料庫中資料表的一個欄位
* 所有的功能，Django會自動產生資料庫存取的API提供使用
* 可以自由切換不同 SQL 語法，只需在setting.py中設定

# 設定資料庫
setting.py檔案  
* 更改成 MySQL，ENGINE 為 'django.db.backends.mysql'
* NAME 更改資料庫名稱
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

# 建立 Model
* 在每個 App 中的 models.py中建立 Model
* 類別class = 模組 Model = 資料表 table
* 預設會幫每一個 Model 加上 id 欄位，並將這個欄位設成 primary key  

<br>
<br>

models.Model 中常用的 Datatype 格式:
|欄位格式|可以使用的參數|說明|
|--|--|--|
|BooleanField||布林值，只有True/False|
|CharField|max_length|字串，儲存較短資料|
|DateField|auto_now: 每次被儲存時自動加入現在日期<br>auto_now_add: 只有在被建立時加入現在日期|日期|
|DateTimeField|(同上)|日期時間|
|DecimalField|max_digits: 可接受的最大位數<br>dicimal_places: 所有位數中佔小數幾位|定點小數數值|
|EmailField|max_length|Email|
|IntegerField||正整數|
|TextField||字串，儲存較長資料|
|URLField|max_length|網址|

<br>
<br>

models.Model 常用欄位選項:
|欄位選項|說明|
|--|--|
|null|空值，預設False|
|blank|空白，預設False|
|default|預設值|
|help_text|求助訊息|
|primary_key|主鍵|
|unique|唯一值，預設False|

<br>
<br>

舉例
```python
# models.py 檔案

from django.db import models

# 建立3個 model
class Topic(models.Model):
    # 類別屬性 = 資料表欄位
    top_name = models.CharField(max_length=264,unique=True)

    def __str__(self):
        return self.top_name

class Webpage(models.Model):
    # 用 class Topic 當作foeign key
    topic = models.ForeignKey(Topic,on_delete=models.CASCADE)
    name = models.CharField(max_length=264,unique=True)
    url = models.URLField(unique=True)

    def __str__(self):
        return self.name


class AccessRecord(models.Model):
    name = models.ForeignKey(Webpage,on_delete=models.CASCADE)
    date = models.DateField()

    def __str__(self):
        # 將 datetime 資料型態改成 str
        return str(self.date)
```

# migrations
Migration 是 Django 將您對模型所做的更改（添加字段，刪除模型等）傳遞到 database schema 的一種方式。

## 同步資料庫
1. 會在 migrations 目錄中建立一個新的 py 檔案，讓 migrate 指令執行時，可以照著這份紀錄更新資料庫，此時尚未將資料同步至資料庫。

    ```shell
    $ python manage.py makemigrations 
    ```
2. 根據上面的紀錄，把 models.py中的欄位寫入資料庫
    ```shell
    $ python manage.py migrate
    ```


# 使用 admin 管理後台
1. 確認 setting.py中的 INSTALLED_APPS 有將 django.contrib.admin 加入(預設有加入)
2. admin.py中設定，register 要在 admin 後台顯示的 models
3. 建立 superuser

舉例，admin.py檔案
```python
# admin.py

from django.contrib import admin
#1 將 App 的 model import 進來
from AppTwo.models import AccessRecord, Topic, Webpage

#2 register models
admin.site.register(AccessRecord)
admin.site.register(Topic)
admin.site.register(Webpage)
```
在 Terminal 建立 superuser
```shell
$ python manage.py createsuperuser
```
建立帳號、email、密碼後即可自動產生 UI

