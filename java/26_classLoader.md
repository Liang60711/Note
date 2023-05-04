## 類加載器
* 作用 : classLoader類加載器的主要作用就是讀取 `.class` byteCode檔案，並轉化成記憶體形式的 `class物件`。

* 每個 Class 物件的內部都有一個 classLoader 屬性來標識自己是由哪個 ClassLoader 加載的。 ClassLoader 就像一個容器，裡面裝了很多已經加載的 Class 物件。
```java
ClassLoader cl = Application.class.getClassLoader()
```

* 類加載器是 JVM 提供的一個類，在 rt.jar 這包裡面。

* JVM 運行實例中會存在多個 ClassLoader，不同的 ClassLoader 會從不同的地方加載字節碼文件。它可以從不同的文件目錄加載，也可以從不同的 jar 文件中加載，也可以從網絡上不同的服務地址來加載。

* JVM 中內置了三個重要的 ClassLoader，分別是 BootstrapClassLoader、ExtensionClassLoader 和 AppClassLoader。

    * `BootstrapClassLoader` : 負責加載 JVM 運行時核心類，這些類位於 `%JRE_HOME%/lib/` 目錄下，如 rt.jar、charset.jar。而常用的內置庫都在 rt.jar 裡面，如 java.util.*、java.io.*、java.nio.*、java.lang.* 。此類為最開始載入的加載器，又稱為`根加載器`。

    * `ExtensionClassLoader` : 負責加載 JVM 擴展類，這些類位於 `%JRE_HOME%/lib/ext` 目錄下，如 內置的 js 引擎、xml 解析器等等，這些庫名通常以 javax 開頭。

    * `AppClassLoader` : 應用程式的類加載器，會加載 Classpath 環境變量裡定義的路徑中的 jar 包和目錄。`我們自己編寫的代碼`以及使用的`第三方 jar 包`通常都是由它來加載的。


<br/>

<br/>

## 參考

> https://zhuanlan.zhihu.com/p/51374915