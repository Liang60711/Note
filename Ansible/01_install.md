# 安裝方式

透過使用 EPEL 去下載

1. 啟用 EPEL 倉庫，Rocky Linux 預設沒有啟用 EPEL（Extra Packages for Enterprise Linux）倉庫，因此需要先啟用。

    ```sh
    dnf install epel-release -y
    ```

2. 安裝 ansible

    ```sh
    dnf install ansible -y
    ```

3. 檢查版本

    ```sh
    ansible --version
    ```

<br/>

<br/>

