# 安裝vEnv
* python 有許多第三方package，因此package更新時容易產生issue，故使用虛擬環境  

建立新虛擬環境 -n, --name
```python
# 安裝最新版本django
$ conda create --name <venv_name> django

# 安裝python指定版本
$ conda create --name <venv_name> python=3.8

# 安裝其他套件
$ conda create --name <venv_name> <package>
```
列出已建立的虛擬環境 -e, --env
```
$ conda info -e
```

進入虛擬環境
```
$ activate <venv_name>
```
退出環境
```
$ deactivate
```
利用 conda 安裝套件
```
$ conda install <package>
```
查看conda版本
```
$ conda -V
```
查看目前環境已安裝套件
```
$ conda list
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
    'first_app'     # 新增App名稱
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

## 建立Views 和 App網址
1. 在App資料夾中的 views.py編輯程式
2. 在urls.py新增網址  

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
