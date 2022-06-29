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
1. JDK SE 15 會自動將執行檔加入環境變數。
    * Java SE = Java Standard Edition (開發執行軟體)
    * Java EE = Java Enterprise Edition (EE 是在 SE 基礎上構建的，開發網站)
    * Java ME = Micro Edition (移動裝置、IOT)
2. Ecplise IDE 目前免付費使用人數最多。

<hr>

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

## Tomcat 安裝 (windows)
1. 下載 Tomcat，並解壓縮。
2. 新增環境變數 `CATALINA_HOME` 為 `C:\Program Files\apache-tomcat-8.5.79`

3. 新增環境變數(path)

    ```sh
    %CATALINA_HOME%\bin
    %CATALINA_HOME%\lib
    ```

4. 這時啟動 `startup.bat` 會報錯，因為在啟動 startup.bat 會調用 `setclasspath.bat`。

    > Neither the JAVA_HOME nor the JRE_HOME environment variable is defined
At least one of these environment variable is needed to run this program

5. `setclasspath.bat` 檔案需新增
    ```sh
    set JAVA_HOME=C:\Program Files\Java\jdk-18.0.1
    set JRE_HOME=C:\Program Files\Java\jdk-18.0.1
    ```

6. 有可能在瀏覽器產生錯誤，這是因為 tomcat 目錄沒有被讀寫的許可權，導致檔案不能被編譯到指定的工作目錄中。

    > java.lang.IllegalStateException: No output folder

7. 找到 tomcat 目錄，右鍵—>內容—>安全性—>編輯，找到Users，將`完全控制`選項打勾。

<br/>

<br/>

## IntelliJ 操作
修改 springboot 版本
1. 開啟 `pom.xml`，修改 `<version>2.3.7.RELEASE</version>` 中的版本。
2. 點選右上角M符號 `Load Maven Changes`
3. 或是右鍵->Maven->Reload project


<br/>

<br/>

## IntelliJ IDEA 快捷鍵
### 快速生成語句

`sout` =  System.out.println();


<br/>

<br/>

### 快捷鍵
`alt`+ `7` 顯示此class大綱 [stack overflow](https://stackoverflow.com/questions/31752352/eclipses-outline-window-equivalent-in-intellij)

`雙擊 shift`: 找文件、類、方法名稱

`ctrl` + `shift` + `f` : 全域搜尋關鍵字
