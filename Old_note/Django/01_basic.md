# 安裝vEnv
* python 有許多第三方package，因此package更新時容易產生issue，故使用虛擬環境  
* 使用 virtualenv
套件安裝
```python
$ pip install virtualenv
```

建立新虛擬環境
```python
$ python3 -m venv env

# env 為環境名稱
```

進入虛擬環境
```python
# windows
$ ./env/Scripts/activate

# mac/linux
$ source ./env/bin/activate
```
退出環境
```
$ deactivate
```
利用 conda 安裝套件
```
$ pip install <package>
```

# 在不同裝置 使用環境
在原裝置可以匯出套件清單
```
$ pip freeze > requirements.txt
```
新裝置中，建立新環境後，輸入
```python
$ pip install -r requirements.txt
```


# 開始Project
進入Env
```
$ activate <venv_name>
```
建立新專案
```
$ django-admin startproject <project_name>
```

## Project內容
* **manage.py**: Python命令檔，提供專案管理的功能

* **\_\_init__.py**: 空白檔案，可讓python視作package
* **settings.py**: 專案的設定檔
* **urls.py**: url配置檔，會用到正規表達式
* **wsgi.py**: 網頁伺服器和 Django 的介面設定檔

連接伺服器
```python
# 進入manage.py所在資料夾
$ cd <project_name>

# 連接server
$ python manage.py runserver
```


# 開始 Application
開始新 App
```
$ python manage.py startapp <app_name>
```
建立 App 後，settings.py 新增App名稱
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    '<app_name>'     # 新增App名稱
]
```


## App 內容
* **\_\_init__.py**: 空白檔案，可讓python視作package
* **admin.py**: 用來定義這個APP在Django後台(Django Administration)的欄位顯示方式
* **apps.py**: App設定檔
* **models.py**: 定義App的資料庫欄位
* **tests.py**: 能夠撰寫App的自動化測試腳本。
* **views.py**: 負責接收瀏覽器的請求，進行邏輯的處理後，回傳執行結果給瀏覽器。

* **migration**: 用來存放 migrations 當修改數據模型時，此文件會自動升級資料庫

## 編輯Views 和 設定App網址
1. 在App資料夾中的 views.py編輯程式
2. 在project.urls.py新增網址  

view.py編輯程式
```python
from django.http import HttpResponse

# Create your views here.
def index(request):
    return HttpResponse('Hello world')
```

urls.py新增網址
```python
from django.contrib import admin
from django.urls import path
from my_app import views    #1 import views.py

urlpatterns = [
    path('index/',views.index,name = 'index'),
    path('admin/', admin.site.urls),
]
```
## path() 傳遞參數和函式對照
語法
```python
path(<url>, <view>, <name = ''>)
```
* **url**: url 中可以傳遞任意數量的參數給 view 中對應的函式，參數型別為字典。
* **view (視圖函數)**: view 指的是處理當前 url 請求的視圖函數。
* **name (別名)**: 對 url 取別名，可在 Django 的任意處(模板)以此別名呼叫。相當於給 url 取了個全域變數名稱。

url 傳入參數，字典型別，用<>包裹，自動型別轉換
```python
# str，不包含/
path('index/<str:a>', views.index, name = 'index')

# int，正整數
path('index/<int:0>', views.index, name = 'index')

# slug，字母、數字、/

# path，任何非空字符串、/
```

## url管理
如果把所有的url放在 project.url.py裡面，顯得不好管理，所以在 my_app 目錄中會新建立一個 urls.py，非主目錄裡面的 project.url.py。

1. project.url.py中的 urlpatterns list 使用 include() 函式
2. my_app.urls.py建立。  

project.url.py檔案
```python
from django.contrib import admin
from django.urls import path, include   # include()函式
from my_app import views

urlpatterns = [
    path('',views.index),       # 首頁
    path('my_app/', include('my_app.urls')),     
    path('admin/', admin.site.urls),
]
```
建立my_app.url.py檔案
```python
from django.urls import path
from my_app import views

urlpatterns = [
    path('', views.index, name ='index'),
    path('index'), view.index2, name = 'index2',
]

# 第一個 url 為 ../my_app/
# 第二個 url 為 ../my_app/index/
```

# 總結

|指令|說明|
|--|--|
|$ django-admin startproject <project_name>|建立Django專案|
|$ python manage.py startapp <app_name>|建立Django APP|
|$ python manage.py runserver|啟動伺服器|
