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

<br/>

<br/>

### ssl_certificate

* 不需要包含根憑證（因為客戶端已經內建信任的根憑證庫）。

* 通常包含 `伺服器憑證` 和 `中繼憑證`（Certificate Chain）。

    * 伺服器憑證 必須放在第一個位置。

    * 中繼憑證可以是 0 個或多個，必須按照憑證鏈的順序排列。

        ```conf
        -----BEGIN CERTIFICATE-----
        <Server Certificate>
        -----END CERTIFICATE-----
        -----BEGIN CERTIFICATE-----
        <Intermediate Certificate>
        -----END CERTIFICATE-----
        ```


### ssl_certificate_key

* 必須是與伺服器憑證匹配的私鑰。

* 私鑰和伺服器憑證應該是配對生成的，如果它們不匹配，Nginx 啟動時會報錯。

### ssl_trusted_certificate

* 指定 OCSP Stapling 所需的 完整信任鏈檔案，用於驗證 OCSP 回應或其他伺服器端功能。

* 必須包含 `中繼憑證` 和 `根憑證`，以確保 Nginx 能完整驗證 OCSP 回應。

    * 中繼憑證必須在前，根憑證必須放在最後。

        ```
        -----BEGIN CERTIFICATE-----
        <Intermediate Certificate>
        -----END CERTIFICATE-----
        -----BEGIN CERTIFICATE-----
        <Root Certificate>
        -----END CERTIFICATE-----
        ```
<br/>

<br/>

## OCSP 配置

OCSP 是一個協議，由 nginx server 發起，向 OCSP server (TWCA憑證中心會有這個 server)，查詢憑證狀態，然後將該狀態綁定到 TLS 握手過程中，傳遞給客戶端（例如瀏覽器）。

狀態有3種:

1. good（有效）

2. revoked（已撤銷）

3. unknown（未知）

若 nginx 沒開啟 OCSP Stapling 功能，則 Client 端要自己向 OCSP server 查詢。

<hr>

Nginx 啟用 OCSP Stapling 的優勢

1. `降低客戶端負擔`：客戶端無需查詢 OCSP Server，Nginx 提供的 OCSP 回應會顯著減少連線延遲。

2. `更好的用戶體驗`：減少因 OCSP 查詢延遲導致的連線等待時間，尤其對高併發服務器至關重要。

3. `減輕 OCSP Server 負載`：客戶端不需要逐一查詢 OCSP Server，減少了 CA 的基礎設施壓力。

<br/>

<br/>

### nginx 配置

```conf
# 開啟 ocsp stapling 功能
ssl_stapling on;
ssl_stapling_verify on;

# 需要多配置此信任鏈
ssl_trusted_certificate /path/to/ca_bundle.crt;

# DNS 查詢 ocsp server
resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 5s;
```

<br/>

<br/>

### 測試
```sh
openssl s_client -connect www.hpicorp.com.tw:443 -status
```

<br/>

<br/>
