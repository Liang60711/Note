## gradle Build 流程
1. gradle會幫整個項目新建立一個 `settings.gradle` 檔案，不管此項目中有多少 module，只會有一個 `settings.gradle`。

2. 針對 `settings.gradle` 中的 include 配置去將各 module 中的 build.gradle 進行的載入。

```groovy
// settings.gradle

rootProject.name = 'project'

include ':module1',':module2'   // : 冒號代表同級目錄 ./ 
project(':module1').projectDir = new File(settingsDir, '../module1')
project(':module2').projectDir = new File(settingsDir, '../module2') 
```

<br/>

<br/>

## 屬性
1. 內置屬性

    ```groovy
    // 取項目名稱
    project.name
    project.property("name")
    ```

2. 擴展屬性 ext，可以定義在 `build.gradle`。

    ```groovy
    // 閉包寫法
    ext {
        hibernateVersion = '3.6.10.Final'
        lombokVersion = '1.18.12'
        cloningVersion = '1.9.0'
        hikaricpVersion = '2.6.3'
    }

    // 一般寫法
    ext.prop1 = "prop1"
    ```
    ```groovy
    // 取屬性
    print(hibernateVersion)

    // 字串中取屬性
    print("prop: ${hibernateVersion}")
    ```

<br/>

<br/>

## 方法
`buildscript` : 當要執行gradle任務時，需要先下載此任務的依賴包，所以須先將依賴包的資訊寫在這，注意: `buildscript 和 app 的依賴是分開的`。
```groovy
// 需要放在 plugin 前面
buildscript {
    repositories {

    }
    dependencies {
        
    }
}
```

<br/>

<br/>

## 依賴配置

>https://tomgregory.com/gradle-implementation-vs-compile-dependencies/


<img src="https://app.lucidchart.com/publicSegments/view/f2f7b3b9-7b90-4950-a571-b7919baff354/image.png" width="80%">

<br/>

<br/>

`dependencies` 區塊內可以定義多種不同的依賴選擇

例如: A 模組依賴了 B 模組，此時 A, B 中各有自己的依賴jar包。

`api` (`舊版compile`): 編譯及運行時都會使用到的依賴，但此依賴可以被其他模組一併載入(B模組的依賴會被A模組載入)。

`implementation` : 編譯及運行時都會使用到的依賴，此依賴只能被自己模組載入(A模組不會載入B模組的依賴)，增加編譯速度。

`compileOnly`(`舊版provided`) : 依賴在編譯有效，運行時無效，如 lombok。

`runtimeOnly` : 依賴在運行有效，編譯時無效，如 jdbc, SLF4J。

<hr>

使用 `compileOnly` 以及 `runtimeOnly` 的原因:
1. 加快編譯速度，不會所有的依賴包都需要編譯。
2. 寫程式時，比較不會用錯class(?)
3. 簡潔的 classpath