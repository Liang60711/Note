## 配置這麼多要去哪裡看


![Snipaste_2023-02-06_20-13-14](https://user-images.githubusercontent.com/63166397/216974852-f330faef-b621-43f2-871b-48dc6b665901.png)
![Snipaste_2023-02-06_20-13-38](https://user-images.githubusercontent.com/63166397/216974870-9c68af6d-6881-467d-903b-37d8bc6ea089.png)


<br/>

<br/>

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

<img src="https://user-images.githubusercontent.com/63166397/204546300-859ff0f9-d002-4ffb-a2d6-257171e4e38a.png" width="50%">

有了Cache後，Status code 為304，並且Request Header會產生 If-Modified-List

<img src="https://user-images.githubusercontent.com/63166397/204546386-c252e9d5-c644-48cb-b858-5b9666fbaf40.png" width="50%">


<br>

<br/>

## Log  配置
```yml
logging:
    level:
        root: info  # 預設是info級別，可調整為debug, error
```
