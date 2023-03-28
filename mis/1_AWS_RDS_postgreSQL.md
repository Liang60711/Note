# PostgreSQL 安裝
1. 下載所需版本
2. 安裝時，地區選擇C，安裝位置默認，資料位置不要跟安裝位置相同。  

#  pgAdmin
建立 database 步驟  
1. 左方Server 右鍵 > create > server group
2. 在新建立的 server group 右鍵 > create > server
3. 輸入 Name 和 Host name/address，如果是本地端可填 localhost。
* 如果是 AWS RDS，Host name/address則填入該資料庫的 endpoint (如: database-1.cbnbxw5so1th.us-east-2.rds.amazonaws.com)。Port號也相同(默認是5432)。Username 為 Master Username。

4. django 專案中 settings.py如下
    ```python
    # 本地端
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': 'DEMO TEST',    # 此為資料庫名稱
            'USER': 'usr',
            'PASSWORD': 'passwd',
            'HOST': 'localhost',
            'PORT': '5432',
        }
    }
    ```
    ```python
    # AWS RDS
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': 'demo_1',       # 在AWS建立資料庫時的名稱
            'USER': 'usr',
            'PASSWORD': 'passwd',
            'HOST': 'database-1.cbnbxw5so1th.us-east-2.rds.amazonaws.com',
            'PORT': '5432',
        }
    }
    ```

5. AWS RDS 建立  
    * 進入 AWS > 搜尋 RDS > create database
    * templates > free tier
    * settings > 填入  master username, password
    * connectivity: public access > Yes (讓pgAdmin有訪問權限)
    * Additional configuration > 填入 database name
6. AWS 設定 
    * Dashboard > Security group rules
    * actions > inbound edit
    * 設定三個 Type: All traffic；三個Source分別為: Custom 0.0.0.0/0、MyIP、Custom ::/0
    * 0.0.0.0/0為IPv4，::/0為IPv6



7. 執行migrate (不用 makemigrations，因為已經有了)
    ```
    heroku run python manage.py migrate
    ```


8. 刪除原有的 database，並新增database，heroku > settings > config vars 輸入
    ```python
    KEY 輸入 DATABASE_URL
    VALUE 輸入 postgres://USERNAME:PASSWORD@database_url_endpoint:PORT/DB_NAME
    ```


