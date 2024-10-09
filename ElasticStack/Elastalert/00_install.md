## Docker 安裝

使用 Docker 安裝，因為普通安裝需要使用 python 3.12 舊版本，環境很混亂。

```sh
docker run -d --name elastalert -p 3000:3000 
-v C:\Users\Administrator\Desktop\elastalert\elastalert.yaml:/opt/elastalert/config.yaml 
-v C:\Users\Administrator\Desktop\elastalert\rules:/opt/elastalert/rules 
ghcr.io/jertel/elastalert2/elastalert2 --verbose
```

`--verbose` : 顯示詳細運行日誌，方便除錯。

`-d` : daemon 執行。

`-p` : `localhosl_port : docker_port`

`-v` : 掛載1個檔案、1個目錄。

```
elastalert
|─ elastalert.yaml
└─ rules
    └─ rule-1.yaml
    └─ rule-2.yaml
```

<br/>

<br/>

## 普通安裝

1. 先安裝pyenv，可以切換版本，目前使用 python `3.12.6`

    ```sh
    # 安裝 pyenv 依賴
    sudo yum install -y git gcc zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel openssl-devel xz xz-devel libffi-devel findutils

    # 安裝 pyenv
    curl https://pyenv.run | bash

    # 配置環境變量
    vim ~/.bashrc
    ```

    `.bashrc` 檔案中加入

    ```vim
    export PATH="$HOME/.pyenv/bin:$PATH"
    eval "$(pyenv init --path)"
    eval "$(pyenv init -)"
    eval "$(pyenv virtualenv-init -)"
    ```

    ```
    # 加載環境變量
    source ~/.bashrc
    ```

2. 使用 pyenv 安裝 python `3.12.6`

    ```sh
    # 安裝指定的 Python 版本
    pyenv install 3.12.6

    # 設置全局版本（全系統範圍）
    pyenv global 3.12.6
    ```

3. 檢查是否安裝成功

    ```sh
    # 查看可以安裝的 Python 版本列表
    pyenv install --list

    # 查看 pyenv 已安裝的版本
    pyenv versions

    # 驗證安裝
    python --version
    ```

4. 安裝 elastalert2

    ```sh
    # 下載 repo 在 /opt/elastalert2
    cd /opt &&
    git clone https://github.com/jertel/elastalert2.git

    # 安裝工具模組
    pip install "setuptools>=11.3"

    # 安裝 elastalert2 ()
    cd /opt/elastalert2 &&
    pip install .
    ```

5. 設定 /opt/elastalert2/examples/config.yaml

6. 設定 /opt/elastalert2/examples/rules/example_frequency.yaml

7. 啟動

    ```sh
    python \
    -m elastalert.elastalert \
    --verbose \
    --config /opt/elastalert2/examples/config.yaml \
    --rule /opt/elastalert2/examples/rules/example_frequency.yaml
    ```

8. 使用 systemd 來管理 daemon 啟動。

    ```sh
    vim /usr/lib/systemd/system/elastalert2.service
    ```

    ```vim
    [Unit]
    Description=Elastalert2

    [Service]
    Type=simple
    PATH=/home/rico/.pyenv/versions/3.12.6/bin:$PATH
    ExecStart=/home/rico/.pyenv/versions/3.12.6/bin/python -m elastalert.elastalert --verbose --rule /opt/elastalert2/examples/rules/example_frequency.yaml --config /opt/elastalert2/examples/config.yaml
    User=rico
    Group=rico

    [Install]
    WantedBy=multi-user.target
    ```

9. 若被 SELinux 擋住，可以配置

    ```sh
    # 檢查 SELinux 的狀態
    sestatus

    # 查看 SELinux 日誌
    sudo cat /var/log/audit/audit.log | grep denied
    ```

    ```sh
    # 放行
    sudo setenforce 0
    ```