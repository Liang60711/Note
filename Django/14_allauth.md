# allauth 套件
### 1. 安裝套件
```
$ pip install django-allauth
```
### 2. 設定 settings
```python
# settings.py

AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',
    'allauth.account.auth_backends.AuthenticationBackend',
]

INSTALLED_APPS = [
    # required for allauth
    'django.contrib.sites',

    # allauth
    'allauth',
    'allauth.account',
    'allauth.socialaccount',

    # providers 
    'allauth.socialaccount.providers.google',
    'allauth.socialaccount.providers.facebook',
]

SITE_ID = 1
```

### 3. 設定 project 的 urls
```python
# urls.py

urlpatterns = [
    path('accounts/', include('allauth.urls')),
]
```

### 4. create database tables
```
$ python manage.py migrate
```

### 5. 建立模板
* templates 位置在 \env\Lib\site-packages\allauth\templates\account  
* 將需要的 templates 放進 project\templates\account 路徑中，直接更改可以覆蓋原 templates  
* 千萬不能直接在套件中更改 templates，否則更新套件時會消失。
```html
{% extends 'base.html' %}
{% load crispy_forms_tags %}
<!-- 載入 allauth 需要的 code -->
{% load socialaccount %}    
{% providers_media_js %}

{% block content %}
<form  method="POST" action="{% url 'account_login' %}">
    {% csrf_token %}
    {{ form|crispy }}
    <input type="submit" value="Login" class="btn btn-info btn-block">
</form>

<!-- google login -->
<button class="btn btn-danger btn-block my-2" type="button">
<a href="{% provider_login_url 'google' method='oauth2' %}">
    Google Login
</a>
</button>

<!-- facebook login -->
<button class="btn btn-primary btn-block" type="button">
<a href="{% provider_login_url 'facebook' method='oauth2' %}">
Facebook Login
</a>
</button>
{% endblock content %}
```

### 6. google 登入
1. [進入Google Developers Console](https://console.developers.google.com/)
2. 左上角 > 新增專案
3. 憑證 > 建立憑證 > OAuth 用戶端 ID
4. 填寫 OAuth 同意畫面
5. 填寫 憑證 > 已授權的 JavaScript 來源 > 填入 http://127.0.0.1:8000
6. 填寫 憑證 > 已授權的重新導向 URI > 填入 http://127.0.0.1:8000/account/google/login/callback/ > 儲存  
7. 進入 admin 後台 > Social applications
8. 新增 > 填入 Provider, Name, Client id > 加入 Sites至Chosen sites
9. 更改 Sites > Domain name: localhost:8000/
10. 更改 Sites >  Display name: localhost


### 7. facebook 登入
1. [進入facebook Developers](https://developers.facebook.com/?locale=zh_TW)
2. 我的應用程式 > 建立應用程式 > 應用程式網域:127.0.0.1
3. [隱私政策網址產生網站](https://www.privacypolicies.com/)
4. 設定 > 基本資料 >取得 應用程式編號、應用程式密鑰
5. 進入 admin 後台 > Social applications
6. 新增 > 填入 Provider, Name, Client id > 加入 Sites至Chosen sites


