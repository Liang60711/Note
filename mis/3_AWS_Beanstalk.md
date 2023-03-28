# AWS beanstalk
可以快速部署網站，將產能集中在研發上。

### 安裝 
需要先安裝 eb 的 CLI
```python
$ pip install awsebcli
```

### 環境
* python 3.6 以上(目前最新到pytohn3.8.5)
* pip
* virtualenv
* awsebcli
* django 2.2

### 步驟
1. 建立名為 eb-virt 的虛擬環境，並啟用虛擬環境

```
$ virtualenv eb-virt
$ eb-virt\Scripts\activate
```

2. 安裝django 2.2
```
$ pip install django==2.2
```

3. 建立環境 requirements.txt
```
$ pip freeze > requirements.txt
```

4. 建立 .ebextensions 目錄，並建立 django.config 
```
option_settings:
  aws:elasticbeanstalk:container:python:
    WSGIPath: <project_name>/wsgi.py
```

5. deactivate 離開虛擬環境


6. 使用 EB CLI 部署網站，需要輸入 AWS_access_ID 和 AWS_secret_KEY
```
$ eb init
```


7. 在 AWS EB server 中建立虛擬環境(需要幾分鐘)
```
$ eb create django-env
```

8. 查看狀態，或是可以直接去 EB 狀態列查看
```
$ eb status
```

9. 設定 django 的 setting.py檔案，
```
# settings.py
# 依照之前 eb init 所選的地區輸入 domain name
ALLOWED_HOSTS = ['eb-django-app-dev.elasticbeanstalk.com']
```

10. 部署
```
$ eb deploy
```

11. 開啟
```
$ eb open
```














