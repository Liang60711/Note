## ES配置

修改配置文件
```sh
vim /etc/elasticsearch/elasticsearch.yml
```


```yml
# 叢集名稱
cluster.name: my-elasticsearch-cluster

# 節點名稱
node.name: node-1

# 網路設置
network.host: 0.0.0.0 # 此設置，任意IP均可訪問

# 資料與日誌路徑
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch

# Elasticsearch 中用來配置節點發現（node discovery）的參數之一。定義了集群中其他節點的主機名或 IP 地址，這些節點將在 Elasticsearch 啟動時用於發現和加入集群。
discovery.seed_hosts: ["127.0.0.1", "[::1]"]

# 用於在一個全新的集群第一次啟動時，指定哪些節點可以被選為主節點
cluster.initial_master_nodes: ["node-1"]
```

記憶體設置 `jvm.options` 檔案中配置 (不可再設定更小，否則可能出問題)
```options
-Xms512M
-Xmx512M
```

啟動後，可以通過訪問 http://localhost:9200/_cluster/health 來檢查集群狀態。

<br/>

<br/>

## Kibana配置

```sh
vim /etc/kibana/kibana.yml
```

配置
```yml
# 配置 Kibana 連接的 Elasticsearch 集群的地址
elasticsearch.hosts: ["http://localhost:9200"]

# Kibana 的伺服器 IP 和端口
server.host: "0.0.0.0"
server.port: 5601
```

<br/>

<br/>

## 查看版本

ES 版本
```sh
/usr/share/elasticsearch/bin/elasticsearch --version
```

Kibana 版本
```sh
/usr/share/kibana/bin/kibana --version --allow-root
```