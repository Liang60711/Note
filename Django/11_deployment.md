# Heroku 部署
## 部署前設定
1. 註冊 Heroku，並安裝 Heroku toolbelt (執行 Heroku CLI 用)
2. 安裝 python 套件
3. 建立 requirements.txt，指定專案的**套件**、**版本**
4. 建立 runtime.txt，指定專案的**執行環境**、**版本**
5. 建立 Procfile，定義專案啟動網站的方式
6. 建立 .gitignore，為了節省伺服器空間，不會把開發時使用的虛擬環境、本機資料庫等上傳
7. 修改 wsgi.py<br>
8. 建立 production_settings.py (舊的不用刪除)，正式上線的環境和開發時設定不同，且使用空白資料庫

<br>

## 2.安裝3個 python 套件
* dj-database-url：heroku處理資料庫的套件
* gunicorn：heroku伺服器的處理套件
* dj-static：heroku處理靜態檔案的套件
```shell
pip install dj-database-url gunicorn dj-static
```

<br>

## 3.建立 requirements.txt  
進入專案資料夾，cmd輸入，則會自動輸出 requirements.txt:
```
$ pip freeze > requirements.txt
```
因為 Heroku 內建 postgreSQL，需檢查有沒有 psycopg2 套件，若無則手動加  

<br>

## 4.建立 runtime.txt
文字檔 runtime.txt 內容:
```txt
python-3.8.5
```

<br>

## 5.建立 Procfile
* P 大寫，檔案無副檔名
* 使用 gunicorn 執行 <project_name>.wsgi 這個模組

檔案內容:
```
web: gunicorn --pythonpath <project_name> <project_name>.wsgi
```

<br>

## 6.建立 .gitignore
* 檔案無副檔名
* 可加入不想上傳的
```txt
*.pyc
__pycache__
db.sqlite3
```

<br>

## 7.修改 wsgi.py<br>
```python
import os

from django.core.wsgi import get_wsgi_application

# 剛安裝的套件
from dj_static import Cling

# <project_name> 注意
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "<project_name>.settings")

# 修改後
application = Cling(get_wsgi_application())
```

<br>

## 8.建立 production_settings.py
```python
# import 舊的 settings
from .settings import *

# Heroku 使用 postgreSQL，修改資料庫設定
import dj_database_url
DATABASES = {
    'default': dj_database_url.config(),
}

# 設定網站正式上線時靜態檔案目錄位置
STATIC_ROOT = 'staticfiles'

# 設定HTTP連線方式
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')

# 允許所有的網域都能瀏覽本網站
ALLOWED_HOSTS = ['*']

# 關閉除錯模式
DEBUG = False
```

<br>

## 最終資料夾 Tree
```shell
my_base
├──project
│   ├── project
│   │   ├── __init__.py
│   │   ├── production_settings.py
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── wsgi.py
│   ├── templates
│   ├── basicApp
│   └── manage.py
├── .gitignore
├── Procfile
├── requirements.txt
└── runtime.txt
```

# git
1. Heroku 登入
2. 新增 Heroku App (手動也可)
3. 指定 Heroku App
4. git 初始化 + commit
5. 設定環境變數
6. 上傳 push


<br>

## 1.Heroku 登入
輸入後會開啟網頁，點選 login
```shell
$ heroku login
```

<br>



## 2.新增 Heroku App (手動也可)
最好是手動，會顯示App名字是否可用
```shell
$ heroku create django-mydemo
```

<br>


## 3.指定 Heroku App
```
$ git init
```
```
$ heroku git:remote -a django-mydemo
```
可以用 git remote -v 檢查
```shell
$ git remote -v
```

<br>


## 4.git 初始化 (與 .gitignore 同一層)
```shell
$ git add .
$ git commit -m "first"
```



<br>

## 5.設定環境變數
確保未來在 Heroku 執行任何指令時，都是使用到部署專用的設定檔
```shell
$ heroku config:set DJANGO_SETTINGS_MODULE=<project_name>.production_settings
```

<br>

## 6.push 到 Heroku
```shell
$ git push heroku master
```