## openssl

* 提供了一組工具，用於生成密鑰、憑證、加密/解密數據，以及其他與加密相關的任務。

* 用途 

    1. 加密與解密：支援多種對稱加密和非對稱加密演算法，例如 AES、RSA、ECDSA 等。

    2. 生成密鑰與憑證：生成公私鑰對、憑證簽名請求（CSR）、自簽名憑證等。
    
    3. SSL/TLS：實現和測試 SSL/TLS 通信，用於建立 HTTPS、FTPS、IMAPS 等安全連接。
    
    4. 數字簽名與驗證：創建和驗證數字簽名，確保數據完整性和身份驗證。
    
    5. Hash 計算：計算數據的摘要（如 SHA-256、MD5），用於驗證文件完整性。
    
    6. 測試與除錯：測試 SSL/TLS 配置是否正確，分析 HTTPS 或其他協議通信。

<br/>

<br/>

<br/>

<br/>

## 生成憑證

1. 先建立一個資料夾，並進入

    ```sh
    mkdir ca && cd ca
    ```

2. 先生成 RSA 私鑰

    ```sh
    # 生成私鑰
    openssl genrsa -out private.key 2048

    # 用 3DES 加密私鑰，需要額外輸入密碼才會產生私鑰
    openssl genrsa -des3 -out private.key 2048
    ```

3. 生成 CSR (Certificate Signing Request，證書簽名請求)

    ```sh
    openssl req -new -key private.key -out server.csr
    ```

    生成 CSR 時，通常需要填寫以下資訊：

    * 國家（C）
    * 省或州（ST）
    * 城市或地區（L）
    * 組織名稱（O）
    * 組織單位名稱（OU）
    * 通用名稱（CN）: 
        * 填寫域名，可以使用通配符 `*.example.com`
    * 電子郵件地址（可選）
    * Challenge Password（可選）

4. 生成自簽名 SSL 憑證

    ```sh
    openssl x509 -req -in server.csr -signkey private.key -out server.crt -days 365
    ```

    * openssl x509: 使用 OpenSSL 來處理 X.509 格式的證書，這是 SSL/TLS 憑證的標準格式。

    * -req: 告訴 OpenSSL 這是一個證書簽名請求（CSR），也就是說，將使用 CSR 來生成憑證。

    * -in server.csr: 指定 CSR 文件。
    
    * -signkey private.key: 使用之前生成的私鑰來簽名 CSR，這樣你可以生成自簽名憑證。

    * -out server.crt: 指定生成的憑證文件名稱，通常是 .crt 或 .pem 格式。

    * -days 365: 設定憑證的有效期，這裡設置為 365 天。


<br/>

<br/>

## nginx 上的設定

當拿到公鑰和私鑰後，nginx 的配置。

```conf
server {
    listen 443 ssl;
    # SSL 憑證（公鑰）
    ssl_certificate /path/to/server.crt;
    # SSL 私鑰
    ssl_certificate_key /path/to/private.key;
}
```

公鑰的生成過程依賴於私鑰，由上可知公鑰的產生，必須

1. 先產生私鑰 `.key`。
2. 用私鑰產生 `.csr` 檔案，並向 CA 機構申請產生 `.crt`。
3. 得到的 `.crt` 就是公鑰。