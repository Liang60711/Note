# 配置

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
http.port: 9200

# 資料與日誌路徑
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch

# 節點角色
node.roles: [master, data, ingest]

# 記憶體設置
bootstrap.memory_lock: true

# 安全設置
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true

# 叢集發現設置
discovery.seed_hosts: ["192.168.1.10", "192.168.1.11"]
cluster.initial_master_nodes: ["node-1", "node-2"]

# GC 日誌設置
jvm.options:
  -XX:+PrintGCDetails
  -XX:+PrintGCTimeStamps
  -Xloggc:/var/log/elasticsearch/gc.log
```

記憶體設置 `jvm.options` 檔案中配置
```options
-Xms2g
-Xmx2g
```

啟動後，可以通過訪問 http://localhost:9200/_cluster/health 來檢查集群狀態。