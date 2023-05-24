## 遞迴
* 遞迴必須要有退出條件
* 缺點: 記憶體消耗大，容易發生`內存洩漏`，次數越多越危險。

舉例: 寫出`階乘`算法
```java
// 迴圈解法
public int factorial1(int num){
    int result = num;
    int i = num - 1;
    do{
        result = result * i;
        i--;
    }while(i>=1);
}

// 遞迴解法
public int factorial2(int num){

    if(num == 1)return 1;
    return num * factorial2(num-1);
}
```