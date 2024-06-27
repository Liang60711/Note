# 設定服務器SSH

以 Ubuntu 來說

1. 確認 ssh 狀態 
    
    ```sh
    sudo systemctl status ssh
    ```

2. 如果沒有安裝則

    ```sh
    apt-get update

    apt-get install openssh-server
    ```

3. 確保防火牆設置允許 SSH 連接

    ```sh
    ufw status
    ```

    如果未啟用防火牆，可以使用以下啟用並添加SSH規則

    ```sh
    ufw enable
    ufw allow ssh
    ```

    如果需要限制來自特定 IP 範圍的 SSH 連接，可以使用以下命令

    ```sh
    ufw allow from ${your_ip_address} to any port 22
    ```

4. 修改 SSH 配置檔案，改完記得重啟

    ```sh
    vim /etc/ssh/sshd_config

    systemctl restart ssh
    ```

5. 測試 SSH 連線

    ```sh
    ssh username@your_server_ip
    ```