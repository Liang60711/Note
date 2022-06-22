## 位移運算符
用移位操作可以大幅提高性能，在底層操作是最快的，移位操作雖快，但是可能代碼不太好理解，因此最好加上註釋。

`左移運算符` `<<`: 左移一位代表乘以2的1次方，左移n位代表乘以2的n次方。

`右移運算符` `>>`:同上，乘以改成除以。

`無符號右移` `>>>`: 二進制的數右移幾位，並在最左邊補0(正數)。
```java
10 << 1 = 20
10 << 3 = 80

6 >> 1 = 3
6 >> 2 = 1

15 >>> 2 = 3 // 0000 1111 向右移兩位 0000 0011
```

<br/>

<br/>

## 補數運算符
將二進制`0`和`1`互補
```java
//  127 = 0111 1111
//    2 = 0000 0010
//    0 = 0000 0000
//   -1 = 1111 1111
//   -3 = 1111 1101
// -128 = 1000 0000
System.out.print(~127);//-128
```

<br/>

<br/>

## 條件運算子
條件式後加上 ? ，若 true 回傳冒號前的值，false 回傳冒號後的值。
```java
(a>b) ? true : false
```


## 邏輯運算子
`& 和 && 的差別`:

* &: 不管怎樣，都會執行`&`符號左右兩邊的程式。
* &&: 只有當符號`&&`左邊程式為真(true)後，才會執行符號`&&`右邊的程式。
* 數字比較

    ```java
    //數字比較會使用二進制，每位進行比對
    // 0000 0011 和 0000 0101 結果是 0000 0001
    System.out.print(3 & 5); // 1
    ```


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