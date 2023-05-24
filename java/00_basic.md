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
