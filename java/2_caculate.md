## 條件運算子
條件式後加上 ? ，若 true 回傳冒號前的值，false 回傳冒號後的值。
```java
(a>b) ? true : false
```


## 邏輯運算子
`& 和 && 的差別`:

* &: 不管怎樣，都會執行`&`符號左右兩邊的程式。
* &&: 只有當符號`&&`左邊程式為真(true)後，才會執行符號`&&`右邊的程式。

`| 和 || 同理`。

<br/>

<br/>





## 流程控制
### for 
```java
for(int i=1; i<10; i++){
    for(int j=1; j=0; j++){
        System.out.println(i+"X"+j+" = "+i*j);
    }
}

// 九九乘法表
```

### foreach
```java
int[] array = {1,2,3,4,5,6,7,8,9,};
for(int i: array) {
    System.out.print(i);
}

// 123456789
```

### switch
* 條件判斷陳述式
* 變數必須要是 int 或 char
* 執行較有效率，如果 case 2 成立，就不會跑 case 1 判斷。
* 相較於執行 if...else if，就會每個條件都判斷。
```java
// 不加 break 會繼續跑下一個 break 為止


int i = 2;

switch(i){
    case 1:
        System.out.println("1");
        break
    case 2:
        System.out.println("2");
        break
    default:
        System.out.println("default");
}
```

### while
* 前側式迴圈
```java
int i = 1;

while(i<10){
    System.out.println(i);
    i++;
}
```


### do while
* 後側式迴圈
* 一定會執行 do {} 裡面的程式再判斷是否繼續迴圈。
```java
int i = 11;

do {
    System.out.println(i);
    i++;
}while(i<10);
```