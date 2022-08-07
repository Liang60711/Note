## 編譯器 compiler
將高階語言(Java, C) 原始碼，轉換為機器碼(Machine code)   
舉例:  
hello.java >> 經過compiler >> hello.class(機器碼)

<hr>

## 環境介紹
### JVM
Java Virtual Machine，Java虛擬機器，是Java跨平台的關鍵，各個OS平台都會有自己的JVM

### JRE
Java Runtime Environment，Java執行環境。

### JDK
Java Development Kit，Java開發工具

### SDK
Software Development Kit，軟體開發工具，JDK就是一個SDK  

### 關係
JDK 包含了 JRE，JRE (底層)又包含了 JVM。

<hr>


## Java運作環境
### Java在系統上運作順序
```
Source Code >>> (經過compiler)
>>> Byte Code (0101的機器碼)
>>> JVM (幫助系統進行翻譯、運作)
>>> Host System
```


<hr>

## Java 執行 && 記憶體大小控制
先用javac來編譯，在執行程式(記憶體最大2048mb，初始256mb)
```
$ javac helloworld.java
$ java -Xmx2048m -Xms256m helloworld
```

<hr>

## 安裝 JDK 和 Eclipse
JDK SE 15 會自動將執行檔加入環境變數。
* Java SE = Java Standard Edition (開發執行軟體)
* Java EE = Java Enterprise Edition (EE 是在 SE 基礎上構建的，開發網站)
* Java ME = Micro Edition (移動裝置、IOT)

<br/>

<br/>

## package命名
```java
package com.apple.cms.vo;
// 命名:  商業, 公司名稱, 項目名稱, 模塊名稱
```

<br/>

<br/>

## 匯入jar檔案
為壓縮檔案，下載lib後，需在編輯器中匯入，以IntelliJ為例:
1. 將 jar 檔案丟入 lib 中。
2. File >> Project Structure。
3. 左邊選 Module，右邊選 Dependencies。
4. 點 `+` ，選 `JARs or Directories`，OK。
5. 可使用 lib 的 Class。

<br/>

<br/>



## IntelliJ 操作
修改 springboot 版本
1. 開啟 `pom.xml`，修改 `<version>2.3.7.RELEASE</version>` 中的版本。
2. 點選右上角M符號 `Load Maven Changes`
3. 或是右鍵->Maven->Reload project



<br/>

<br/>

## IntelliJ 快捷鍵
`alt`+ `1` 定位目前檔案在目錄位置

`alt`+ `7` 顯示此class大綱 [stack overflow](https://stackoverflow.com/questions/31752352/eclipses-outline-window-equivalent-in-intellij)

`ctrl` + `E` : 顯示最近打開的檔案


`雙擊 shift`: 找文件、類、方法名稱

`ctrl` + `shift` + `O` : 刪除沒用到的 import class

`ctrl` + `shift` + `L` : 格式化程式的排版

`ctrl` + `shift` + `F` : 全域搜尋關鍵字

`ctrl` + `shift` + `向左鍵` : 跳轉上一層

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