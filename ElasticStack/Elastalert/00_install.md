## 安裝

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