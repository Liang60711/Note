## 安裝

1. 新建來源庫

    ```sh
    sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
    ```
    ```sh
    vim /etc/yum.repos.d/elastic.repo
    ```
    貼上以下，版本可以更改為 8.x
    ```sh
    [elastic-7.x]
    name=Elastic repository for 7.x packages
    baseurl=https://artifacts.elastic.co/packages/7.x/yum
    gpgcheck=1
    gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
    enabled=1
    autorefresh=1
    type=rpm-md
    ```

2. 安裝 

    ```sh
    yum install filebeat -y
    ```

3. 啟動

    ```sh
    sudo systemctl start filebeat
    sudo systemctl enable filebeat
    ```
4. 檢查
    ```sh
    systemctl status filebeat
    ```