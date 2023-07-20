## Apache 文檔

> 文檔首頁 https://httpd.apache.org/docs/2.4/

> ReWrite模組 https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html


<br/>

<br/>

## ReWrite 模組
在轉導時，加入一些條件，可以讓符合條件的 HTTP 請求轉發到相對應的port或路徑。

需要先載入模組
```sh
LoadModule rewrite_module modules/mod_rewrite.so
```

<br/>

基本語法如下

```sh
RewriteCond [TestString] [CondPattern] [flags]
RewriteRule [match_uri] [rewrite_uri] [flags]
```

* RewriteCond, RewriteRule: 為配置的關鍵字，用來寫條件

* TestString: 進行條件測試的輸入字符串。它可以是 URL、HTTP Header、QueryParams、服務器變量或與請求相關的任何其他信息，有相對應的語法去取這些變數。

* CondPattern: 符合的條件，可以是指定字串或Regex，若符合就會觸發下方 RewriteRule。

* Flag: 標誌用於修改條件的行為。以中括號為語法，並用逗號分隔。一些常見的標誌包括以下: 

    * `[NC]` : Non Case-sensitive，不區分大小寫
    * `[OR]` : 邏輯 OR
    * `[AND]` : 邏輯 AND
    * `[L]` : Last，代表成功執行這個 Rule 後就會停止，不繼續往下執行，常用在 RewriteRule 後面。

    * `[R]` : Redirect，代表用轉址的方式轉到新的網址，預設是使用 302 Status Code 做轉導。也可以使用 `[R=301]`，也可以回傳 400、200、404 等的 Status Code，通常會跟 [L] 一起代表結束，也是除錯常用的 Flag

    * `[F]` : Forbidden，回傳 403 狀態碼。

    * `[P]` : Proxy，會將請求交給 mod_proxy 模組處理，使用 P flag，本身就包含了 L flag，表示請求立即通過代理推送，並且不會考慮任何後續規則。

    * `[QSA]` : Query String Append，代表保留網址尾端帶的 GET 參數，沒使用 flag 的預設是會把參數去掉的

    * `[QSD]` ： Query String Discard，與 QSA 相反的作用，apache v2.4 才有。

    > Apache document，更多的Flag: https://httpd.apache.org/docs/2.4/rewrite/flags.html


<br/>

<br/>

## Proxy 模組
需要開啟內建模組
```sh
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_module modules/mod_proxy_http.so
```

`ProxyPass` : 將請求轉發給誰進行處理

`ProxyPassReverse` : 和ProxyPass指令配合使用，若有一個請求訪問 __demo.com/products__，此請求會被轉發給 __localhost:8080/products__ ，當 302 轉發至 __localhost:8080/login__ 處理請求時，當沒有設置 ProxyPassReverse 時，用戶端就會收到轉導到 __localhost:8080/login__，導致無法訪問，所以需要設置轉發代理。
```sh
<VirtualHost *:80>
   ServerName  domainName.com
    Redirect   / https://domainName.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName  domainName.com

    ProxyPass /  http://localhost:8080/ # 將請求轉發給本地端8080port
    ProxyPassReverse /  http://localhost:8080/ # 將 / 作為本地端8080port的代理，轉發時就會以 / 作為轉發根目錄
</VirtualHost>
```


<br/>

<br/>

## WebSocket 設定
設定 httpd.conf 檔案
```sh
# 以下內建 Module 需要開啟
LoadModule rewrite_module modules/mod_rewrite.so
LoadModule ssl_module modules/mod_ssl.so
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_module modules/mod_proxy_http.so
```

```sh
# 如果要寫在 httpd-vhosts.conf 需要引入
Include conf/extra/httpd-vhosts.conf

# 配置
<VirtualHost *:80>
   ServerName  domainName.com
    Redirect   / https://domainName.com/
</VirtualHost>


<VirtualHost *:443>
    ServerName  domainName.com
    # 開啟 ReWrite 功能
    RewriteEngine On

    # 條件設定: 若 HTTP Header Upgrade 的 value 等於 websocket
    RewriteCond %{HTTP:Upgrade} =websocket [NC]
    # 使用 P flag 將請求交給 proxy，並轉導到該路徑
    RewriteRule /(.*)           ws://localhost:8080/$1 [P,L]
    RewriteCond %{HTTP:Upgrade} !=websocket [NC]
    RewriteRule /(.*)           http://localhost:8080/$1 [P,L]

    # 使用 P
    ProxyPassReverse / http://localhost:8080/

    SSLEngine On
    SSLProtocol -all +TLSv1.2
    SSLCipherSuite ALL:!aNULL:!ADH:!eNULL:!LOW:!EXP:!NULL:!RC4:!RC2:!DES:!3DES:!SHA:!SHA256:!SHA384:!MD5+HIGH:+MEDIUM
    SSLCertificateFile /etc/httpd/ssl/Insynerger.crt
    SSLCertificateKeyFile /etc/httpd/ssl/Insynerger.key
    SSLCertificateChainFile /etc/httpd/ssl/gd_bundle-g2-g1.crt
</VirtualHost>
```