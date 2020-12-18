# QuerySet API
需要進入 Django shell
```
$ python manage.py shell
```


舉例，app/models.py
```python
from django.db import models

# one
class Topic(models.Model):
    top_name = models.CharField(max_length=264,unique=True)

    def __str__(self):
        return self.top_name

# many
class Webpage(models.Model):
    # foeign key
    topic = models.ForeignKey(Topic,on_delete=models.CASCADE)
    name = models.CharField(max_length=264,unique=True)
    url = models.URLField(unique=True)

    def __str__(self):
        return self.name
```
需要先手動載入函式，非自動
```python
>>> from app.models import Topic, Webpage
```

## Create
新增: 需要save()
```python
>>> t = Topic(top_name = 'News')
>>> t.save()
# save() 是把資料寫入至資料庫
```


新增: 不用save()
```python
>>> Topic.objects.create(top_name = 'News')
# create() 自帶寫入功能
```

新增: 有 Foreign Key 的資料
```python
# 若未建立資料，則要先save()寫入資料庫
t = Topic(top_name = 'News')
t.save()

# 新增 Foreign Key 的資料
w = Webpage(topic = t, name='Alex', url='www.google.com')
w.save()
```

新增: get_or_create(object, created) ，get不到會建立一筆資料(true)，會回傳一個 tuple
```python
t = Topic.objects.get_or_create(top_name = 'News')[0]
t.save()
```


## Read
**all()**: 選取所有
```python
>>> Topic.objects.all()
```
**get(\*\*kwargs)**: 返回符合條件的唯一一筆資料(id)，如果找不到符合條件的資料、或是有多筆資料符合條件，都會產生 exception
```python
>>> Topic objects.get(id=1)
```
**filter(\*\*kwargs)**: 回傳符合條件的陣列(QuerySet型態)。如果找不到任何資料則會返回空陣列。
```python
>>> Topic objects.filter(top_name = 'News')
```
```python
>>> type(Topic objects.filter(top_name = 'News'))
# <class 'django.db.models.query.QuerySet'>
```
**contains**: 相當於SQL中 WHERE...LIKE '%%'，寫法需要加上雙底線
```python
# 雙底線加在col後面
>>> Topic.objects.filter(top_name__contains = 'News')
```



## Update
**update(\*\*kwargs)**: kwargs必須是類陣列型態，可先用 filter() 篩選出來確認後再進行更改，get()篩選則是非陣列型態，會報錯。
```python
# 先篩選存成物件
>>> t = Topic.objects.filter(top_name__contains = 'News')

# 再用update()修改，物件 t 會回存到 Topic.objects
>>> t.update(top_name = 'Games')
```

## Delete
與 update() 相同，透過篩選後刪除
```python
>>> t = Topic.objects.filter(top_name = 'Games')

>>> t.delete()
```
全部刪除
```python
>>> Topic.objects.all().delete()
```

# One to Many Relation
* Django.db.models 中，沒有 OneToManyCharField() ，故使用 ForeignKey() 來處理一對多關係。
* 資料參考 [Reference](https://carsonwah.github.io/15213187968523.html)

<br>

## 1.建立 One-To-Many Model
```python
# models.py
# 每個 Restaurant 可以有多於一個 Food

from django.db import models

class Restaurant(models.Model):
    name = CharField(max_length=256)

class Food(models.Model):
    restaurant = ForeignKey(Restaurant, 
                            on_delete = models.CASCADE, 
                            related_name = 'foods')
    
    name = CharField(max_length=256)
```

<br>

## 2.取得一個 Parent 的所有 Child
舉例來說就是，找一個 Restaurant 的所有 Food  

### 方法1：Query
```python
# 取得一間restaurant
restaurant = Restaurant.objects.get(id=1)

# restaurant__id 為 restaurant 的 id column
Food.objects.filter(restaurant__id=restaurant.id)
# <QuerySet [<Food: 冷飯>, <Food: 涼麵>]>
```

### 方法2：Child Set
語法: 先把 child 的物件 lowercase 再加上 _set

```python
# 取得一間restaurant
restaurant = Restaurant.objects.get(id=1)

restaurant.food_set.all()
# <QuerySet [<Food: 冷飯>, <Food: 涼麵>]>
```
備註：關於Manager
* Restaurant.objects　是一個　Manager，用來管理對　restaurant　的　query，官網文件[Manager Reference](https://docs.djangoproject.com/en/2.0/topics/db/managers/)。  
* restaurant.food_set 也是一個 Manager，但它管理對「屬於這間 restaurant 的 food」的 query。

<br>

### 方法3：自訂 field name
可以更直觀的選取物件，與 _set 的用法是相同的
```python
# models.py
class Food(models.Model):
    restaurant = ForeignKey(Restaurant, 
                            on_delete = models.CASCADE, 
                            related_name = 'foods')
    
    name = CharField(max_length=256)
```
```python
restaurant = Restaurant.objects.get(id=1)

# 可直接使用 related_name
# 等價 restaurant.food_set.all()
restaurant.foods.all()
```