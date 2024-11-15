## 概述

1. Docker 官方的開源項目。

2. 負責實現對容器集群的快速編排，可以管理多個容器並且組成`一個應用`。

3. 需要定義一個 `docker-compose.yml` 配置文件，先定義多個容器之間的調用關係，一鍵啟動。

4. docker建議，一個容器指運行一個服務。

<br/>

<br/>

## 安裝
3步驟

```sh
# 下載
sudo curl -L "https://github.com/docker/compose/releases/download/{version}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 權限
chmod +x /usr/local/bin/docker-compose

# 查看版本
docker-compose --version
```

<br/>

<br/>

