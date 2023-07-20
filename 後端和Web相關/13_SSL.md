## 憑證

> https://matthung0807.blogspot.com/2021/03/https-what-is-root-certificate.html

`根憑證`: 由於 HTTPS 是利用網站提供的憑證(certificate)來驗證網站的真實性，而網站憑證(server certificate)的源頭都是由`可信任的憑證頒發機構(trusted certificate authority，Root CA)` 所簽發。而Root CA自身的憑證就叫作`根憑證root certificate`。

`中繼憑證` : 中繼憑證是由下級 CA 簽發的憑證，而不是由根憑證簽發的。中繼憑證的作用是將下級 CA 簽發的數字憑證(Digital Certificate)和根憑證之間建立連接，以增加數字憑證的可信度。中繼憑證本身沒有權限簽發其他下級 CA 或數字憑證，它只是一個連接下級 CA 和根憑證之間的橋樑。

`Root Store` : Root Store 資料庫通常包含在瀏覽器和其他應用程式中，用來驗證憑證是否有效。瀏覽器中的 SSL Root Store 資料庫通常由瀏覽器廠商管理和更新，因此不同的瀏覽器可能會使用不同版本的 SSL Root Store 資料庫。

`使用SSL情境` : 
當客戶端瀏覽HTTPS網站時，網站會提供其憑證 (server/leaf certificate) 及發行單位的中繼憑證 (intermediate certificate) 供客戶端驗證，客戶端根據憑證的`信任鍊(chain of trust)`層層去 root store 搜尋中繼憑證的public key來解密網站憑證的數位簽章(digital signature)並與憑證的雜湊函式(hash function e.g. SHA-256)算出的雜湊值比較是否相同來驗證憑證的真實性。而信任鍊最上層的憑證就是根憑證。


<br/>

<br/>

## 在 Apache 中使用 SSL 憑證
* 憑證分為 `.crt` 副檔名的憑證文件，另一個是 `.key` 副檔名的私鑰文件。

* `SSL 憑證`

    SSL 憑證是由一個或多個數字簽名機構（CA）簽發的數字證書，它用來驗證網站的身份，確保網站上的敏感資訊可以安全地傳輸。憑證文件通常使用 .crt 或 .pem 副檔名。

* `私鑰`

    私鑰是一個加密的數字密鑰，它用來加密和解密 SSL 通訊中的數據。私鑰只能由擁有者持有，不應該洩漏給其他人。私鑰文件通常使用 .key 或 .pem 副檔名。

* `中介憑證(鏈式憑證)`

    是由一個或多個中介證書機構（Intermediate CA）簽發的數字證書，用於證明 SSL 憑證的真實性和合法性。


步驟 : 

1. 在 `/etc/httpd/ssl` 路徑中放入 SSL憑證和私和私鑰 
2. 在 `/etc/httpd/conf` 路徑中修改 `httpd.conf` 配置檔案

    ```conf
    <VirtualHost *:80>
        ServerName  demo.example.com
        Redirect   / https://demo.example.com/
    </VirtualHost>


    <VirtualHost *:443>
        ServerName  demo.example.com

        ProxyPassReverse / http://localhost:8080/

        SSLEngine On    #開啟SSL
        SSLProtocol -all +TLSv1.2   # 使用TLSv1.2 protocol
        SSLCipherSuite ALL:!aNULL:!ADH:!eNULL:!LOW:!EXP:!NULL:!RC4:!RC2:!DES:!3DES:!SHA:!SHA256:!SHA384:!MD5+HIGH:+MEDIUM   # 加密演算法
        SSLCertificateFile /etc/httpd/ssl/Insynerger.crt    # 憑證路徑
        SSLCertificateKeyFile /etc/httpd/ssl/Insynerger.key # 私鑰路徑
        SSLCertificateChainFile /etc/httpd/ssl/gd_bundle-g2-g1.crt # 中介憑證路徑
    </VirtualHost>
    ```