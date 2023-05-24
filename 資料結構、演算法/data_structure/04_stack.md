## stack 棧
使用 `Stack` 類實作。

<br/>

<br/>

`push()` 加入元素  
`pop()` 移除元素
```java
Stack<String> s = new Stack<>();
s.push("black");
s.push("yellow");
s.push("flower");
s.push("white");
```
`peek()` 取最上面一筆
```java
s.push("black");
s.push("yellow");
s.push("flower");
s.push("white");

s.peek();// white
s.pop();
s.peek();// flower
```
