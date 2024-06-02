# switch case 的寫法 不需寫 break

Before
```java
String name = "xxx";
String alias;

switch (name) {
    case "aaa":
        alias = "AAA";
        break;
    case "bbb":
        alias = "BBB";
        break;
    case "ccc":
        alias = "CCC";
        break;
    default:
        alias = "XXX";
}
```

After，switch 可以有回傳值

```java
// java 17

String name = "xxx";
String alias = switch (name) {
    case "aaa" -> "AAA";
    case "bbb" -> "BBB";
    case "ccc" -> "CCC";
    default -> "XXX";
};
```

若
1. 在 case 有多個選項 (使用逗點隔開)
2. 在 case 中有業務邏輯 (使用 `yield` 關鍵字)

```java
// java 17

String name = "xxx";
String generation = switch (name) {
    case "周瑜", "諸葛亮" -> {
        System.out.println("三國");
        yield "三國";
    };
    case "成吉思汗", "忽必烈" -> {
        System.out.println("元朝");
        yield "元朝";
    };
    case "韓信", "劉邦" -> {
        System.out.println("漢朝");
        yield "漢朝";
    };
    default -> {
        System.out.println("未知");
        yield "未知";
    }
};
```

增加了 switch 對物件的匹配

```java
// java 17
String result = switch (o) {
    case Integer i -> String.format("Integer i : %s", i);
    case Long l -> String.format("Long l : %s", l);
    case Double d -> String.format("Double d : %s", d);
    case String s -> String.format("String s : %s", s);
    default -> o.toString();
};
```

<br/>

<br/>

# 字串拼接

使用3個雙引號，可以創造多行字串的區塊

```java
// java 15
String html = """
<html>
    <body>
        <h1>Hello, World!</h1>
    </body>
</html>
""";
```

Java 17 增強了兩個 `\` 和 `\s` 轉譯符號。

* `\` 符號 : 用在行尾，將2行連接為1行(省略中間的空白鍵)

* `\s` 符號 : 代表單個空白字串

<br/>

```java
// java 17

String html = """
<html>\
    <body>\
        <h1>Hello, World!</h1>\
    </body>\
</html>
""";

// 相當於
String html = """
    <html><body><h1>Hello, World!</h1></body></html>
""";
```

使用 `\s` 範例
```java
// 如果單單使用空白鍵，會用不出來，必須使用 \s 
String html = """
<html>
    <body>
        <h1>Hello, \s World!</h1> 
    </body>
</html>
""";
```