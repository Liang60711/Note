
## 陣列 Array
* 陣列只能存放相同資料型態；例如:String array
* 陣列是物件
* 宣告一定要有長度。

<br>

### 陣列宣告1
```java
int[] array_1 = {1, 2, 4, 8, 16};
```

### 陣列宣告2
```java
int[] array_2 = new int[5];

// 宣告長度為5
```

### 陣列宣告3
```java
int[] array_2 = new int[] {1,2,3,4,5};

// 不用宣告長度
```


### 二維陣列宣告1
```java
int[][] array_3 = {{1, 2}, {3, 4, 5}}       // 不一定要正方形矩陣

```

### 二維陣列宣告2
```java
int[][] array_4 = new int[2][2]     // 表示 2X2 int陣列

array_4[0][0] = 1;                  // 賦值
array_4[1][1] = 1;

array_4[2][2] = 1;                  // 超出範圍會報錯
```

### 陣列取值
```java
int a = array_1[0]      // a = 1
```

### 陣列複製
```java
int[] num1 = new int[2];
int[] num2 = Arrays.copyOf(num1, 2);    // 參數1=要複製的陣列，參數2=複製長度
```

不能使用
```java
int[] num2 = num1       // 這個意思是將 num1 的 reference 交給 num2
```

### 陣列 print
```java
// 因為 array是物件，所以需要轉型才能print
import java.util.Arrays;

String[] array = {"id", "name", "age"};

System.out.println(Arrays.toString(array))      // ["id", "name", "age"]
```

<br/>

<br/>

## 可變參數(varargs)
允許在調用方法時傳入不定長度的參數，是 Java 的一個語法糖。

1. 可變參數只能作為函數的最後一个參數，前面可以有也可以沒有其他參數。
2. 由於可變參數必須是最後一個參數，所以一個函數最多只能有一個可變參數。

```java
// 可變參數，直接輸入值，與陣列相同意思
public void foo(int... x){
    for(int i :x){
        System.out.println(i);
    }
}


// 調用
foo(1,2,3);
foo(1,2,3,4,5,6);
```


<br/>

<br/>


