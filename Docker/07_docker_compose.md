## 概述

1. Docker 官方的開源項目。

2. 負責實現對容器集群的快速編排，可以管理多個容器並且組成`一個應用`。

3. 需要定義一個 `docker-compose.yml` 配置文件，先定義多個容器之間的調用關係，一鍵啟動。

4. docker建議，一個容器指運行一個服務。

5. 優點: 

  - 容易上手。
  - 開發友好，主要用於開發和測試階段。
  - 支援環境變數以動態配置服務

6. 缺點:

  - 不支援跨主機的容器協作。
  - 缺乏內建的容錯和自動擴展功能。



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

## 基礎命令

```sh
docker-compose -h # 查看幫助

docker-compose up # 啟動所有服務

docker-compose up -d # 啟動所有服務，並後臺運行

docker-compose up -f  /path/to/docker-compose-file # 啟動所有服務，指定 docker-compose.yml 檔案路徑

docker-compose down # 停止並刪除容器、network；預設 volume、image 不會一起刪除，除非使用 --volumes 或 --rmi 參數

docker-compose exec <服務id> # 進入容器實例內部

docker-compose ps # 查看當前 docker-compose 所有容器

docker-compose top # 查看當前 docker-compose 所有容器的 process

docker-compose logs <服務id> # 查看容器輸出日誌

docker-compose config # 檢查配置

docker-compose config -q # 檢查配置，有問題才會輸出

docker-compose restart # 重啟服務

docker-compose start # 啟動服務

docker-compose stop # 停止服務
```

<br/>

<br/>

## 範例

在 springboot 的 application.yml 會產生 ip 寫死的情況，如 mysql 的 url，所以進行 docker-compose.yml 的編寫時。


所以 springboot-app 服務，可以將資料庫連線資訊寫在以下任意一個位置

1. 寫在 docker-compose.yml `environment 屬性`裡面。

2. 寫在 springboot 的 `application.yml` 裡面。

兩者都不可以寫死 IP，都要使用服務名稱來取代 IP。


```yaml
version: "3.8"

# 建立服務，共有3個
services:
  springboot-app:
    image: test:11
    container_name: springboot-app # 如果這行不寫，此服務對外暴露的服務名稱就預設是 springboot-app
    ports:
      - "8080:8080"
    volumes:
      - /app/microService:/data
    environment:
      - SPRING_DATASOURCE_URL=jdbc:mysql://mysql:3306/db2024?useUnicode=true&characterEncoding=utf-8&useSSL=false
      - SPRING_DATASOURCE_USERNAME=root
      - SPRING_DATASOURCE_PASSWORD=root
      - SPRING_DATASOURCE_DRIVER-CLASS-NAME=com.mysql.cj.jdbc.Driver
      - SPRING_REDIS_HOST=redis
      - SPRING_REDIS_PORT=6379
    networks:
      - app-network
    depends_on:
      - redis
      - mysql
    
  redis:
    image: redis:6.0.8
    container_name: redis # 如果這行不寫，此服務對外暴露的服務名稱就預設是 redis
    ports:
      - "6379:6379"
    volumes:
      - /app/redis/redis.conf:/etc/redis/redis.conf
      - /app/redis/data:/data
    networks:
      - app-network
    command: redis-server /etc/redis/redis.conf

  mysql:
    image: mysql:8.0.40
    container_name: mysql # 如果這行不寫，此服務對外暴露的服務名稱就預設是 mysql
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: 'root'
      MYSQL_ALLOW_EMPTY_PASSWORD: 'no'
      MYSQL_DATABASE: 'db2024'
    networks:
      - app-network
    command: --default-authentication-plugin=mysql_native_password # 解決外部無法訪問

# 建立 network
networks:
  app-network:
    driver: bridge

# 建立 volume
# Docker 會管理這些卷的位置和存儲，並且會存儲在 Docker 的默認位置（例如在 Linux 系統中通常是在 /var/lib/docker/volumes/ 路徑下），不需要顯式指定宿主機的路徑，Docker 會自動處理。
volumes:
  mysql_data:
```


如果將 springboot-app 服務的資料庫連線資訊寫在 application.yml

```yaml
spring:
  datasource:
    # url 直接使用服務名稱，不可寫 IP
    url: jdbc:mysql://mysql:3306/db2024?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
  redis:
    host: redis # 直接使用服務名稱，不可寫 IP
    port: 6379
```