# Docker Network
>Container 是個封閉的空間，有些功能開發是需要與其他 Containers甚至是外界串接的，例如 PHP, MYSQL, Apache, Nginx 彼此溝通，因此Docker network因應不同需求發展出了多種類別的network。

<br/>

## 分為以下幾種 Network Drivers

### 0. Network driver 解釋
為了讓 docker 支援多種網路類型，docker networking subsystem 被設計成是以可抽換 driver 的形式來運作，可根據使用者需求置換不同的 driver 來達到不同的網路設定目的；以下5個 driver 已經是預設存在的：

### 1. Bridge networks
>Docker 預設就是使用此網路模式，這種網路模式就像是 NAT 的網路模式，例如實體主機的 IP 是 192.168.1.10 ，會對應到 Container 裡面的 172.17.0.2，在啟動 Docker 的 service 時會有一個 docker0 的網路卡就是在做此網路的橋接。


### 2. Overlay networks
>

### 3. Host networking
>Host networking 使 container 的隔離性質消失，在該 container當中可以直接使用例如 localhost 來找尋到主機上的 port 或其他資源。

### 4. Macvlan networks


### 5. None networks
>不設定 container 的網路，因此 container 無法對外通訊。

<br/>

<br/>

# 查看指令
從主機查看 nginx container 的 IP 地址
```sh
docker container inspect --format "{{ .NetworkSettings.IPAddress}}" nginx
```

選擇 container 的 Network 類型
```sh
# --net=type
# 有 none, container, host, bridge, overlay 類型
docker container -it --net=none nginx
```

<br/>

<br/>

# Network 指令
顯示當前 networks
```sh
docker network ls
```

檢查指定 network
```sh
docker network inspect [network_name]
```

建立 network
```sh
# bridge is default drive
docker network create --drive [driver_type] [network_name]
```

將 network 連接至 container
```sh
# Connect a running container to a network
docker network connect [network_name] [container_name]

# Connect a container to a network when it starts (啟動 container 時，可以連接至 network)
docker container run -d --name new_nginx --network my_local_network nginx
```

將 network 與 container 中斷連線
```sh
docker network disconnect
```


</br>

</br>

## 練習
新建立一個 bridge network，在此新建立兩個 nginx1 nginx2，並使用 nginx1 ping nginx2
```sh
#1 建立 network
docker network create -d bridge my_network

#2 建立 container 
docker container run -d --name nginx1 --network my_network nginx:alpine
docker container run -d --name nginx2 --network my_network nginx:alpine

#3 檢查
docker container ls
docker network inspect my_network

#4 執行 CLI
docker container exec -it nginx1 ping nginx2
```

<br/>

<br/>

# container 相互連接
`--link` Add link to another container；其實用 `docker-compose` 更簡單
```sh
# --link [container_name] [container]
docker container run -it --name my_nginx --link: db_01 mysql nginx
```