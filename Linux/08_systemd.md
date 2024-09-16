# Systemd

### 啟動服務模板

```sh
[Unit]
Description=My Spring Boot Application
After=syslog.target

[Service]
User=myapp   # 指定執行者權限，建議一個服務一個專門的使用者
WorkingDirectory=/opt/myapp # 指定工作目錄，若沒有指定，啟用 myapp.jar 時，會讀不到該目錄下的 yml 檔，只能讀到 jar 中的檔案。
ExecStart=/usr/bin/java -jar /opt/web/web.jar & # 啟用服務指令
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```

<br/>

<br/>

### 查看類
直接查看 filebeat.service 的配置內容，就不用再去 `find / -name 'filebeat.service'`

```sh
# 直接查看配置內容
systemctl cat filebeat.service
```
