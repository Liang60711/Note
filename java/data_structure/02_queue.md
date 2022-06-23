## Queue
佇列是一種線性的資料結構，通常使用`LinkedList`作為queue介面的實作類。


<br/>

<br/>


`add()` 加入元素  
`size()` 返回長度
```java
Queue<String> queue = new LinkedList<>();
queue.add("black");
```
`peek()` 取佇列的root  
`poll()` 移除一個元素

```java
queue.add("black");
queue.add("yellow");
queue.add("flower");
queue.add("white");

queue.peek(); // black
queue.poll();
queue.peek(); // yellow
```