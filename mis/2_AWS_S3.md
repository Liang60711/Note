# S3 儲存網站的 media files 和 static files
1. 進入 AWS 主控台，並選擇S3服務
2. 點選 Create bucket
3. 輸入 Bucket name
4. 選擇 Block public access (bucket settings)，如果要讓外部可以新增image，ACL 可以不用 block。
* ACL，access control list，用來管控一些 IP 可以操作(如:CRUD)的權限。
5. 安裝 django-storages, boto3   

    ```python
    pip install django-storages
    ```
    ```python
    pip install boto3
    ```
6. 在settings.py設定參數
    ```python
    # AWS IAM 使用者的 ACCESS_KEY_ID 和 SECRET_ACCESS_KEY
    # AWS_SECRET_ACCESS_KEY 忘記無法找回，只能重新create一組
    AWS_ACCESS_KEY_ID = '****************'
    AWS_SECRET_ACCESS_KEY = '*********************'
    # AWS S3 BUCKET_NAME
    AWS_STORAGE_BUCKET_NAME = 'liang-demo-1'


    # django-storage package
    # boto3 為 AWS SDK(software development kit) in python
    # 檔案系統使用外部服務的話，需要有 storage backend
    AWS_S3_FILE_OVERWRITE = False   # 同 static 檔名時是否覆蓋
    AWS_DEFAULT_ACL = 'public-read'
    DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
    
    # 如果要存 static files 可以使用
    STATICFILES_STORAGE = 'storages.backends.s3boto3.S3StaticStorage'
    ```

7. 上傳檔案，不會出現在本地端 media_url 中，而會直接出現在 S3。
8. 如果要 建立 IAM user來管理權限的話，可以搜尋 IAM 服務 > add user > 選擇Programmatic access > 點選Attach existing policies directly > 選擇AmazonS3FullAccess > create user