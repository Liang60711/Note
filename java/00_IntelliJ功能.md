
## IntelliJ 快捷鍵
`alt`+ `1` 定位目前檔案在目錄位置

`alt`+ `7` 顯示此class大綱 [stack overflow](https://stackoverflow.com/questions/31752352/eclipses-outline-window-equivalent-in-intellij)

`ctrl` + `E` : 顯示最近打開的檔案

`ctrl` + `R` : 替換 replace

`ctrl` + `D` : 等於 ctrl c + ctrl v

`ctrl` + `W` : 選取該程式區塊，可往區塊外擴增

`ctrl` + `Q` : 查看[方法/變數/類]的詳細資料

`ctrl` + `H` : 查看此類的繼承層級Hierarchy

`ctrl` + `+` : 展開程式碼

`ctrl` + `-` : 摺疊程式碼

`ctrl` + `tab` : 切換當前視窗

`ctrl` + `F9` : 使用dev-tool後直接進行熱部屬刷新

`雙擊 shift`: 找文件、類、方法名稱

`ctrl` + `shift` + `O` : 刪除沒用到的 import class

`ctrl` + `shift` + `I` : 優化縮排

`ctrl` + `shift` + `L` : 格式化程式的排版

`ctrl` + `shift` + `F` : 全域搜尋關鍵字

`ctrl` + `shift` + `U` : 大寫轉小寫

`ctrl` + `shift` + `向左鍵` : 跳轉上一層

`ctrl` + `shift` + `F8` : 中斷點列表

`Ctrl` + `Alt` + `Shift` + `U` : UML Diagram 查看(重要)。

`alt` + 左右鍵 : 切換視窗

`F12` : 切換回上一個開啟的視窗

> https://www.gushiciku.cn/pl/pom2/zh-tw

<br/>

<br/>

## IntelliJ 功能
* 檢視資料庫 : `Database`功能中，在檢視table頁面，可點選右上角眼睛 `view as`-> `transpose` 倒置table。

* 查詢路由 : 若要查詢此class中，所有 route list，可以點選下方 `endpoint` tab。

* 外掛套件 : File >> Settings >> Plugins

<br/>

<br/>

## debug 功能

1. 上排工具: 

    * `show excecution point` : 跳回斷點檔案。
    * `step over` : 逐行執行。
    * `step into` : 跳進function。
    * `evaluate expression` : 執行功能(好用)。
    * `trace current stream chain` : 追蹤stream、追蹤lambda(好用)。


2. 左排工具:

    * `view breakpoint` : 查看所有斷點、是否開啟exception斷點。
    * `mute breakpoint` : 打開 / 關掉所有斷點。

3. 斷點上按右鍵 : 可設定觸發斷點條件。

    ```java
    // condition 
    i == 3
    ```


<br/>

<br/>

## 使用 IDEA IntelliJ 配置熱部署
* 前因後果 : 因為 springboot 的 server 是內嵌的，所以不能從外部去監控，應該要去 Spring IoC 容器中添加監控程式，而這個程式就是 `devtools`，負責監控是否程式碼有更動。

* 使用 Eclipse STS 版本就不用此配置，可直接使用，IDEA需要設置。

* 步驟: 注意第3點是2022版本才改到此位置

    1. 加入 `devtools` 依賴

        ```groovy
        // gradle
        dependencies {
            compileOnly(group: 'org.springframework.boot', name: 'spring-boot-devtools', version: '2.7.2')
        }
        ```
        ```xml
        <!-- maven 要多設定 plugin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
        </dependency>

        <build>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <configuration>
                        <fork>true</fork>
                        <addResources>true</addResources>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        ```
    
    2. 勾選 File >> Settings >> Compiler >> Build project automatically

    3. 勾選 File >> Settings >> Advanced Settings >> `Allow auto-make to start even if developed application is currently running`

    4. 重開 IntelliJ，啟動 application，看到 `[restartedMain]` 表示成功。

    5. 以上是自動熱部屬，觸發條件是失去 IDEA 編輯器視窗焦點 5 秒後 (所以可以去點瀏覽器)

    6. 如需要手動熱部屬時，按下 ctrl + F9，Build Project。

    7. 若要使用 resources 熱部屬，如 jsp 檔案，可參考

        [link text](../../java/framework/jsp/00_.md#idea-中jsp-若需要設置熱部屬)
        

<br/>

<br/>

## 關於 熱部屬
通常啟動 server 時，會做以下2個動作

1. 重啟(Restart) : 自定義開發代碼，包含類、頁面、配置文件，載入 `restart類加載器`

2. 重載(Reload) : Jar包，載入`base類加載器`

重點: 熱部屬只會做 `重啟(Restart)` 的動作，不加載Jar資源。

<br/>

<br/>

## 熱部屬的配置
可以修改那些檔案類型需要熱部屬，那些不用，以下是不觸發熱部屬的目錄
* /META-INF/maven
* /META-INF/resources
* /resources
* /static
* /public 
* /templates

```yml
spring: 
  devtools:
    restart:
      exclude: static/**,public/**,application.yml # 逗號後沒有空格
```

<br/>

<br/>

## 使用系統屬性關閉熱部屬 (重要)
一般使用 yml 屬性關閉熱部屬，生產環境絕對不可能會有使用熱部屬的情況(程式碼不會變動)
```yml
spring: 
  devtools:
    restart:
      exclude: static/**,public/**,application.yml # 逗號後沒有空格
      enabled: false # 關閉熱部屬
```

但有一種情況是當在 yml 檔案中設置關閉，但在較高層級的 yml 檔案又開啟，會造成管理上的不方便(雖然熱部屬開啟對生產環境無效)。

因此使用系統屬性，權限高過任何四級 yml 檔案
```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {

        // 設置系統屬性(屬性名稱與 properties key 相同)
        System.setProperty("spring.devtools.restart.enabled", "false");
        SpringApplication.run(DemoApplication.class, args);
    }
    
}
```

可以參考官網的屬性優先級別(有更新過，以前版本順序相反)，系統屬性排在第9

<img src="../../_image/Snipaste_2023-03-08_21-10-14.png">

<br/>

<br/>

## 自定義 註解格式
註解可以自定義縮排
```java
class Test {

    // 這是預設的註解格式
//  private String name;


    // 改成這樣比較美觀
    // private Integer age;
}
```

<img src="../_image/Snipaste_2023-06-14_11-48-07.png">