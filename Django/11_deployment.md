# Heroku 部署
## 1.部署前設定
1. 註冊 Heroku，並安裝 Heroku toolbelt (執行 Heroku CLI 用)
2. 安裝 git

<br>

## 2.安裝 python 套件
* gunicorn：heroku伺服器的處理套件
* whitenoise: 處理靜態檔案的套件
```shell
pip install gunicorn
```
```shell
pip install whitenoise
```
whitenoise 設定
```python
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',   # 多加這一行
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

```

<br>

## 3.建立 requirements.txt  
進入專案資料夾，cmd輸入，則會自動輸出 requirements.txt:
```
$ pip freeze > requirements.txt
```
<br>

## 4.建立 runtime.txt
文字檔 runtime.txt 內容:
```txt
python-3.8.7
```
<br>

## 5.建立 Procfile
* P 大寫，檔案無副檔名
* 使用 gunicorn 執行 <project_name>.wsgi 這個模組

檔案內容:
```
web: gunicorn <project_name>.wsgi
```

<br>

## 6.建立 .gitignore
* 檔案無副檔名
* 可加入不想上傳的
```txt
*.pyc
__pycache__
```

<br>

## 7.靜態檔案設定
設定蒐集路徑
```python
# settings.py
STATIC_ROOT = os.path.join(BASE_DIR, 'static_root')
```
蒐集靜態檔案
```
$ python manage.py collectstatic
```

<br>

## 8.設定 Django ALLOWED_HOSTS
```python
# settings.py
ALLOWED_HOSTS = ['*']
```

<br>

## 9.Heroku 登入
輸入後會開啟網頁，點選 login
```shell
$ heroku login
```
<br>


## 10.新增 Heroku App (手動也可)
最好是手動，會顯示App名字是否可用
```shell
$ heroku create django-mydemo
```
<br>

## 11.初始化、指定 Heroku App
```
$ git init
```
```
$ heroku git:remote -a <repo_name>
```
可以用 git remote -v 檢查
```shell
$ git remote -v
```
<br>


## 4.git 加入檔案
```shell
$ git add .
$ git commit -m "deploy"
```


<br>

## 5.push 到 Heroku
```shell
$ git push heroku master
```