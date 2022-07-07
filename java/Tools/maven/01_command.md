## 指令
`mvn clean` : 刪除 src 底下的 target 目錄。

以下指令是連貫的 (若運行deploy則會從compile開始跑): 

`mvn compile` : 編譯 spring boot 程式。

`mvn test` : 運行單元測試。

`mvn package` : 打包成 jar檔，存放在 `src/target` 路徑下。

`mvn install` : 將 jar檔放到 `local倉庫`。

`mvn deploy` : 將 jar檔上傳到 `remote倉庫`。

<br/>

<br/>

## IntelliJ
1. 點選右邊 Maven tab 。
2. 點選 Lifecycle 目錄。
3. 可執行相關 maven 指令。

<br/>

<br/>

## mvnw 檔案
* 是 mvn wrapper 的縮寫，當作業系統有安裝 JDK 卻沒有安裝 Maven 時，依舊能夠通過 mvnw 指令運行 Maven 相關功能的工具。

* 使用上將原本使用 mvn 的指令的內容都改為 mvnw 即可。

* wrapper 資料夾內的檔案，是當發現系統沒有安裝 Maven 時會使用到的相關程式。