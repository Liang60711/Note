## 靜態資源配置

* 靜態資源的訪問，默認是 `/resources/**`，如果有 `webapp/WEB-INF` 目錄，則使用此路徑作為根目錄。

* 源碼在 `WebMvcAutoConfiguration` 這個自動配置類中。

* 補充: 如果`API路徑(controller route)`與靜態資源相同，則會先去訪問`API路徑`(硬要測試的話)。



```yml
spring:
    mvc:
        # 使用路徑前綴
        static-path-pattern: /prefix/** #這樣訪問路徑即為/prefix/resources/**
    
    web: # <-- 舊版本沒有web階層
        resources:
            # 更改靜態資源路徑根目錄(所以根目錄為resources/rootPath)
            static-locations: classpath:/rootPath/
            # 禁用靜態資源路徑配置，效果就是所有的靜態資源都無法訪問
            add-mappings: true  #預設為true，禁用為false
            # 設置靜態資源緩存時間(11000秒)
            cache:
                period: 11000
```