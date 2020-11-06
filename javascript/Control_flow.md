# if.. else if.. else
```java
if (<condition>) {
    //statement;
} else if (<condition>) {
    //statement;
} else {
    //statement;
}
```

# while
```java
while (<condition>) {
    //statement
}
```

# do while 
與while不同的是，迴圈至少被執行1次，再判斷條件
```java
do {
    //statement
} while (<condition>)
```

# for 
```java
for (<initialExp>;<condition>;<incrementExp>){
    //statement
}

// <initialExp>   初始條件
// <condition>    判斷條件
// <incrementExp> 更新條件
```
舉例
```javascript
for (var i = 0; i < 5; i++){
    console.log(i)
}
// 回傳 01234
```
for...in 和 for...of，對象為array  
for...in 輸出屬性名稱(key)，for...of 輸出值(value)
```javascript
var alpha = ['A','B','C']

for (i in alpha){
    console.log(i)
}
// 輸出 0 1 2


for (j of alpha){
    console.log(j)
}
// 輸出 A B C
```


