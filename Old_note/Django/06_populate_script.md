# 使用 Faker 套件產生隨機資料
文件URL: https://faker.readthedocs.io/en/master/  

安裝
```shell
$ pip install Faker
```
產生假資料
```python
from faker import Faker

fake = Faker()

# 利用 fake 物件產生各種資料
fake.name()
fake.address()
fake.text()
```
產生在地化資料
```python
from faker import Faker

fake = Faker('zh_TW')
fake = Faker(['it_IT', 'en_US', 'ja_JP'])

for _ in range(10):
    print(fake.name())
# 鈴木 陽一
# Leslie Moreno
# Emma Williams
```
# 創建產生資料的腳本
1. 在 project 下建立一個 script.py檔案 (與 manage.py同層級)
2. 設定環境條件後執行

舉例，script.py檔案
```python
#1 如果想通過自行創建的python文件在django項目中使用django的models就需要設定django的環境
import os
import django

os.environ.setdefault('DJANGO_SETTINGS_MODULE','ProTwo.settings')
django.setup()



#2 撰寫腳本
import random
from AppTwo.models import Topic,Webpage,AccessRecord
from faker import Faker

fake = Faker()
topics=['Search','Social','Market','News','Games']

# one
def add_topic():
    t = Topic.objects.get_or_create(top_name=random.choice(topics))[0]
    t.save()    
    return t

def populate(n=5):

    for i in range(n):

        # 建立假資料物件
        fake_url = fake.url()
        fake_date = fake.date()
        fake_name = fake.company()

        top = add_topic()
        # many
        webpage = Webpage.objects.get_or_create(topic=top, name=fake_name, url=fake_url)[0]
        access = AccessRecord.objects.get_or_create(name=webpage, date=fake_date)[0]

# 執行 (新增20筆資料)
if __name__ == '__main__':
    print('populating script')
    populate(n=20)
    print('populating complete')

```