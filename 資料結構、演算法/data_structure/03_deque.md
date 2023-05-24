## Deque 雙端佇列
與queue相同，是線性的collection，支援在`首尾兩端`插入和移除元素，解決佇列過長時插入速度太慢。


<br/>

<br/>

`add()` 加入元素  
`size()` 返回長度
```java
Deque<String> deque = new LinkedList<>();

deque.add("black");
```
`getFirst()` 取第一個元素  
`getLast()`  取最後一個元素  
```java
deque.getFirst();
deque.getLast();
```

