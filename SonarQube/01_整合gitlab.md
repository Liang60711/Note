## 整合

這邊整合的是自建的 gitlab server。

1. 啟動 Sonarqube server，並可以進入。

2. 交換應用程式的 token

    * 在 gitlab server 生成一個 token，讓 sonarqube 可以載入專案

        <img src='../_image/Snipaste_2025-01-17_09-44-38.png'>

        <br/>

        權限勾選4個 api, read_api, read_user, read_repository。


    * 在 sonarqube 生成一個 token，讓 gitlab runner 在執行時，有權限可以執行登入 sonarqube server。

        <img src='../_image/Snipaste_2025-01-17_09-50-33.png'>

        <br/>

        以上是生成全域的登入token，或是依照專案訪問權限來生成token，以下範例。

        <img src='../_image/Snipaste_2025-01-17_09-55-59.png'>

        <br/>

2. 導入專案或是在 sonarqube 首頁建立專案。

    <img src='../_image/Snipaste_2025-01-17_09-41-27.png'>