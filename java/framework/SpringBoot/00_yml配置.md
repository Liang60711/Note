靜態資源的訪問，默認是 `/resources/**`，如果需加上前綴，可以使用這個配置。

補充: 如果`API路徑(controller route)`與靜態資源相同，則會先去訪問`API路徑`。
```yml
spring:
    # 使用前綴
    mvc:
        static-path-pattern: /prefix/** #這樣訪問路徑即為/prefux/resources/**
    
    # 更改靜態資源路徑根目錄(所以根目錄為resources/rootPath)
    resources:
        static-locations: classpath:/rootPath/
```