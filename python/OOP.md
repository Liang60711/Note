# Class 類別
* 類別(Class)
* 物件(Object)
* 屬性(Attribute)
* 建構式(Constructor)
* 方法(Method)

```python
# 類別
class Dogs():
    # 建構式
    def __init__(self, breed, name):
        self.breed = breed  # breed屬性
        self.name = name    # name屬性
    # 方法
    def wooh(self):
        print(f"My dog's breed is {self.breed} and name is {self.name}.")
# 物件
myDog = Dogs('tab','Alex')
```

# 實體屬性 類別屬性
### 區別
* **實體屬性**:必須隨物件的生成來建立，透過點(.)的語法或在建構式中所生成的屬性。各物件的實體屬性(Instance Attribute)各自獨立。修改某一個物件的實體屬性值時，不會影響到其他物件。

* **類別屬性**:定義在類別層級的屬性，也就是在建構式之外的屬性。可以不需要建立物件，直接透過類別名稱存取。各物件共享類別屬性(Class Attribute)值，也就是說當修改類別屬性值時，每一個透過此類別所建立的物件(Object)，都會受到影響。

```python
class Dogs():
    sex = 'male'    # 類別屬性
    
    def __init__(self, breed, name):
        self.breed = breed  # 實體屬性
        self.name = name    # 實體屬性
    
    def wooh(self):
        print(f"My dog's breed is {self.breed} and name is {self.name}.")
```
舉例，實體屬性
```python
myDog = Dogs('tab','Alex')
yourDog = Dogs('tab','Candy')

myDog.name = 'Boy'   # myDog屬性更改不會影響到yourDog屬性
```
舉例，類別屬性
```python
# 可以不需建立物件進行更改
Dogs.sex = 'Female'  # 類別屬性更動會影響所有物件屬性值

print(myDog.sex)     # Female
print(yourDog.sex)   # Female
```

# class 繼承
class B 可以繼承class A 的所有method
```python
class A():
    def __init__(self):
        pass
    def hello():
        print('hello')


class B(A):
    def __init__(self):
        pass

objectB = B()
objectB.hello()     # 還是可以使用
```
# 函數\_\_str__() 
* \_\_str__( )函数，在建立的物件被print出來時，可以回傳函數內容
```python
class book():
    
    def __init__(self, name, author, pages):
        self.name = name
        self.author = author
        self.pages = pages

b = book('Python', 'Alex', 100)
print(b)        # 只會回傳記憶體位置
```
使用__str__()
```python
class book():
    
    def __init__(self, name, author, pages):
        self.name = name
        self.author = author
        self.pages = pages
    def __str__(self):
        return [self.name, self.author, self.pages]

b = book('Python', 'Alex', 100)
print(b)        # ['Python', 'Alex', 100]
```

# 函數\_\_len__() 
* \_\_len__()函數可以定義 len() 函數在執行時回傳的東西
```python
class book():
    
    def __init__(self, name, author, pages):
        self.name = name
        self.author = author
        self.pages = pages
    
    def __len__(self):
        return self.pages

b = book('Python', 'Alex', 100)
print(len(b))   # 100
```













