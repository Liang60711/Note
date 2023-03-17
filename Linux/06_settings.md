## IP 和 hostname 映射檔案

* 在路徑 `/etc/hosts` 中做設定，當輸入 example1.com 或 example2.com 這兩個 domain name 時，會先找此檔案中的映射，並轉導到對應的 IP，如果都找不到，就會去找 DNS server，`此映射只限本主機有效`。


    ```conf
    127.0.0.1 localhost
    192.168.10.14 example1.com example2.com
    ```

* 在 windows 也有相同的映射檔案 `C:/windows/system32/drivers/etc/hosts`