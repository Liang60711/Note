# nginx

## nginx 版本
1. Nginx 開源版本

2. Nginx 商業版本

3. OpenResty 版本，基於 Nginx 與 Lua 的高效能 Web 平台；裡面有許多 Lua 庫。

4. Tengine 版本，為淘寶的開源專案。

<br/>

<br/>

## 檢查 vm 中，防火牆有沒有開啟 80 port 

若使用的是 `firewalld`
```sh
# 1.檢查防火牆狀態
systemctl status firewalld

# 2. 檢查 80 port 是否開放
firewall-cmd --list-all

# 3. 如果列表中沒有顯示 http，將 http 加入
sudo firewall-cmd --zone=public --add-service=http --permanent
sudo firewall-cmd --reload
```

若使用的是 `iptables` 或是 `UFW (Uncomplicated Firewall)`，則使用其他指令。

<br/>

<br/>

## 啟動/關閉

```sh
# 啟動
nginx 

# 快速停止，直接殺掉線程
nginx -s stop 

# 優雅停止，在停止前不會再接收請求，但若有未完成的請求如下載，會持續做完
nginx -s quit

# 重新加載配置
# 其實 nginx 會再開啟一個新線程，並載入新的配置，當舊線程的請求處理結束，就會將舊線程kill，達到優雅的效果
nginx -s reload
```

但基本上還是會使用 systemd 來啟動 

<br/>

<br/>

## 安裝預設目錄、文件

配置目錄：`/etc/nginx`

日誌目錄：`/var/log/nginx`

專案目錄：`/usr/share/nginx/html`

二進制文件(執行文件)：`/usr/sbin/nginx`

<br/>

<br/>

## nginx 應用

* 短網址服務：

    1. 透過輸入短網址到某域名如 `demo.320e4d0c`，短網址的 nginx 會接收 demo.* 的所有請求。

    2. nginx轉發給如tomcat應用程式，從資料庫中獲取完整網址。

    3. 獲取完整網址後，redirect 回給 client。

* 反向代理：

    1. 例如現在有個域名和其他子域名，如 demo、demo.sub1、demo.sub2。
    
    2. nginx 可以接收所有域名，並依照子域名轉發給其他後端服務器。
    
    3. 而可以接收所有域名的服務器，可稱為反向代理服務器。