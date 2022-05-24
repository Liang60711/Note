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

## Hello World
```java
package test;

public class HelloWorld {
    // attribute

    // method
	public static void main(String[] args) {
		System.out.println("Hello World");
	}

}
```
類別 class 
1. 通常字首大寫
2. 命名與檔名相同
3. 需有 {}

方法 method 
1. 一切從 main 方法開始
2. 沒有回傳值 void
3. 輸入參數 String[] args

修飾子 modifier 
* 存取權限修飾子 Access Control Modifiers
    * public
    * private
    * protected
    * default
* 非存取權限修飾子 Non-Access Modifiers
    * static: 不用產生實體，就可以直接使用方法、屬性
    * final
    * abstract
    * synchronized

package 類似資料夾，檔案必須放在 package 名稱下才能執行程式
```java
$ javac HelloWorld.java

$ java HelloWorld
```

屬性 attribute  
1. 可提供給方法使用
2. 可以不用提供初始值
3. 會有修飾子