## 概述

CIG 指的是3種服務的統稱:

* CAdvisor: 監控蒐集

* InfluxDB: 數據存儲

* Grafana: 可視化圖表

有點類似 ELK，將 docker 容器產生的日誌和紀錄儲存在 DB 中，並用可視化的服務顯示。

<br/>

<br/>

## 使用 CIG，不使用 Portainer 的理由

* `查看過去宿主機的紀錄`: Portainer 查看主機的 stats(CPU、Memory) 都是實時的數據，當想看過去的數據時，就沒有提供此功能。

* `健康指標、過線預警功能`

<br/>

<br/>

## 替代方案

由於 CAdvisor 也支持與其它服務的集成，所以不一定要將數據存在 InfluxDB，也可以存在 `Redis`、`Elasticsearch`、`Kafka`。