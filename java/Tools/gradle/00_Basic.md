## gradle 介紹
1. 解決 maven 中 `pom.xml` 因為依賴越來越多所造成的麻煩。
2. gradle 使用 groovy 語言寫設定檔，語法較為簡潔。

<br/>

<br/>


## 目錄結構
需要先建立環境變數

* `GRADLE_HOME` 為安裝gradle的目錄:   `D:\gradle-3.1`。
* `GRADLE_USER_HOME` 預設從此目錄找jar包而不是中央倉庫: `D:\maven_repository`


```sh
└─project
   ├─.gradle          # gradle執行訊息
   ├─src              # source code
   |  ├─main
   |  |   ├─java
   |  |   ├─resources
   |  |   └─webapp    # 放頁面元素，如 js, css, img,jsp, html
   |  └─test
   |      ├─java
   |      └─resources
   ├─build.gradle     # 添加依賴的文件
   └─settings.gradle  # 針對 module 的全局配置
```

<br/>

<br/>

## build.gradle 範例
1. 每一個 jar 包都有三個基本組成:  `group`, `name`, `version`
2. `testCompile` 此屬性為 jar包的`作用域`，表示 jar 包只在測試時起作用。
```groovy
group 'com.rico'
version = "1.0"
apply plugin: "java"

sourceCompatibility = 1.8   // jdk版本

/**
 * repositories: 指定所使用的倉庫，mavenCentral()表示中央倉庫(非第三方庫)，會預設從本地倉庫找，若沒找到，則會由中央倉庫下載到本地倉庫
 */
repositories {
    mavenLocal()
    mavenCentral()
}

/**
 * dependencies: 所有專案用到的 jar 包都放置在此
 */
dependencies {
    testCompile  group: 'junit', name: 'junit', version: '4.12'
    implementation group: 'org.springframework', name: 'spring-core', version: '5.3.22'

}
```


<br/>

<br/>

## groovy 
1. 可以使略句尾分號 `;`
2. 可以省略小括號 `()`
3. 定義變數

    ```groovy
    def i = 18 // 弱型別
    
    print i
    ```

4. 添加/取得 list 元素

    ```groovy
    // 添加
    def list1 = ['a', 'b']
    list1 << 'c' // 添加進list

    // 取得 (使用index)
    list1.get(2)
    ```

5. 閉包 closure

    ```groovy
    def b1 = {
        println "hello b1"
    }

    // 定義方法
    def method1(Closure closure) {
        closure()
    }

    // 調用方法
    method1(b1)
    ```