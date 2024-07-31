## 概述

* ssl 憑證可以是單一網域，也可以是多網域共享一個憑證。

* 多網域共享一個憑證，即 `通配符證書（Wildcard Certificate)`。

    * 例如：`*.example.com`，同時保護多個網域。


<br/>

<br/>

## 證書配置

```conf
server {
    listen                  443 ssl;
    server_name             example.com.tw;
    
    # example 1，兩個都是 pem 檔
    ssl_certificate         /etc/letsencrypt/live/fullchain.pem;
    ssl_certificate_key     /etc/letsencrypt/live/www.hpicorp.com.tw/privkey.pem;


    # example 2，crt 和 key 檔
    ssl_certificate         /etc/nginx/ssl/hpicorp_2023/server.crt;
    ssl_certificate_key     /etc/nginx/ssl/hpicorp_2023/server_no.key;
}
```