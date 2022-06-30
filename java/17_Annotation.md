## 註解 Annotation
* 可以理解為是一種分散式的元數據，與源碼綁定。
* 備註: 
    * metadata: 後社資料，有關資料的資料，主要是描述資料屬性(property)的資訊。

<br/>

<br/>

## Java SE Annotation

1. @Override: 重寫方法。
2. @Deprecated: 標示過期方法，但還是可以使用。
3. @SuppressWarnings: 用來消除編輯器警告。

<br/>

<br/>


## 元註解 Meta-Annotation
目的是用來`註解其他註解`，其中依照功能分為以下4種:

1. `@Target` : 指定註解可以使用在哪邊(類、方法、參數)。

    |@Target()選項|修飾在哪|
    |--|--|
    |ElementType.TYPE|類、介面、枚舉、註解|
    |ElementType.FIELD|成員變數、枚舉的常量|
    |ElementType.METHOD|方法|
    |ElementType.PARAMETER|方法的參數|
    |ElementType.CONSTRUCTOR|構造方法|
    |ElementType.LOCAL_VARIABLE|區域變數|
    |ElementType.ANNOTATION_TYPE|註解(元註解)|
    |ElementType.PACKAGE|套件|


<br/>

<br/>


2. `@Retention` : 定義了註解的生命週期。

    |枚舉值|生命週期|
    |--|--|
    |`RetentionPolicy.SOURCE`|只在源代碼中有效果，不會編譯進class文件|
    |`RetentionPolicy.CLASS`|除了在源代碼有效果，也會編譯進class文件，但是在運行期是無效果，為@Retention的默認值|
    |`RetentionPolicy.RUNTIME`|源代碼到運行期一直存在。永久保存，可以透過反射獲取這個註解的信息|

<br/>

<br/>

3. `@Documented` : 讓註解的資訊可以顯示在 Java API 文件上。


<br/>

<br/>

4. `@Inherited` : 被標記此註解的父類，可以將註解一併繼承給子類。

<br/>

<br/>

5. `@Repeatable` : 原本註解在同一個位置只能出現一次，@Repeatable可以在同個位置使用多次。


<br/>

<br/>

## 自定義 Annotation
1. 使用`@interface`為關鍵字
2. 定義成員變數，可使用`default`關鍵字設定預設值

    ```java
    public @interface MyAnnotation {

        // 定義變數需加上小括號
        public String name();
        // 給變數預設值
        public int age() default 20;
        // 定義陣列
        public String[] foods();
        // 定義枚舉類型
        public Color color();
    }
    ```
    ```java
    // 自定義枚舉
    public enum Color {
        RED, BLUE, YELLOW
    }
    ```


<br/>

<br/>



