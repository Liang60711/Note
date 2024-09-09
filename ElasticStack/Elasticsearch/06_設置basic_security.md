## 概述
Elasticsearch 的 Basic Security 是在免費基礎授權（Basic License）下提供的一組安全功能。這些功能為 Elasticsearch 叢集提供了基本的安全保護，讓你在不需要付費的情況下使用一些關鍵的安全功能。


<br/>

<br/>

## 設置

1. `elasticsearch.yml`

    ```yml
    xpack.security.enabled: true
    xpack.security.transport.ssl.enabled: true
    xpack.license.self_generated.type: basic
    ```


2. 建立密碼，預設會有6組帳號

    ```sh
    cd /usr/share/elasticsearch/

    ./bin/elasticsearch-setup-passwords interactive
    ```


    |帳號|密碼|權限說明|
    |--|--|--|
    |elastic|@WSX3edc|管理者權限|
    |apm_system|@WSX3edc|允許 APM 伺服器對 ES 操作數據|
    |kibana_system|@WSX3edc|允許 Kibana 對 ES 操作數據|
    |logstash_system|@WSX3edc|允許 Logstash 對 ES 操作數據|
    |beats_system|@WSX3edc|允許 Beats 對 ES 操作數據|
    |remote_monitoring_user|@WSX3edc|跨叢集的遠端監控使用|





<br/>

<br/>

3. `filebeat.yml`

    ```yml
    output.elasticsearch:
    hosts: ["localhost:9200"]
    username: "beats_system"
    password: "@WSX3edc"
    ```

<br/>

<br/>

4. `kibana.yml`

    ```yml
    elasticsearch.username: "kibana_system"
    elasticsearch.password: "@WSX3edc"
    ```

5. 重啟服務

    ```sh
    systemctl restart elasticsearch
    systemctl restart filebeat
    systemctl restart kibana
    ```