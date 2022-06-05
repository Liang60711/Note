## 多執行緒 生命週期
>https://ithelp.ithome.com.tw/articles/10158833  

>https://java.4-x.tw/java-15/java-15-4

## 產生多執行緒方式
1. 繼承(extends) Thread 這個類的子類，並且覆寫 `run()`，缺點是`無法繼承其他類`。
2. 實作(implements) Runnable interface，並實作 `run()`。

<br/>

<br/>

第一種使用 `extends Thread`
```java
public class TestThread extends Thread{

    private String name;

    public TestThread(String name) {
        this.name = name;
    }

    public void run(){
        System.out.println(name);
    }
}
```
類可以直接 new，執行使用 start()
```java
public static void main(String[] args){

    // main()自己的thread
    System.out.println("main starts");

    TestThread t1 = new TestThread("thread1");
    TestThread t2 = new TestThread("thread2");
    TestThread t3 = new TestThread("thread3");

    // 各自跑各自的thread
    t1.start();
    t2.start();
    t3.start();

    System.out.println("main ends");

}
```

第二種使用 `implements Runnable`
```java
public class TestRunnable implements Runnable{

    private String name;

    public TestRunnable(String name){
        this.name = name;
    }

    public void run() {
        System.out.println(name);
    }
}
```
用法需使用 new Thread()，與第一種不同
```java
public static void main(String[] args){

    Thread t1 = new Thread(new TestRunnable("thread1"));
    Thread t2 = new Thread(new TestRunnable("thread2"));
    Thread t3 = new Thread(new TestRunnable("thread3"));

    // 各自跑各自的thread
    t1.start();
    t2.start();
    t3.start();

}
```

<br/>

<br/>

## yield() 方法
1. 使用 yield() 會讓 thread 停留在生命週期中 `runnable` 的狀態。
2. 使用 yield() 會讓此線程讓出資源，

```java
// 實作 runnable 的 run()
public void run(){
    System.out.println(name + "start");

    if ("thread1".equals(name)){
        
        Thread.yield();
    }

    System.out.println(name + "running");
    System.out.println(name + "end");
}
```

<br/>

<br/>

## sleep() 方法
1. 會將 thread 進入生命週期的 `blocked` 狀態
2. sleep() 放入毫秒作為參數。
```java
// 實作 runnable 的 run()
public void run(){
    System.out.println(name + "start");

    if ("thread1".equals(name)){
        // block 2秒
        Thread.sleep(2000);
    }

    System.out.println(name + "running");
    System.out.println(name + "end");
}
```

<br/>

<br/>

## join() 方法
* 當下 thread 執行到 join 時，會先讓 join 的執行緒先執行(插隊)，當 join 執行完成後，才會繼續執行當下執行緒。

```java
// join() 會出 InterruptedException
public static void main(String[] args) throws InterruptedException {

    System.out.println("main starts");

    Thread t1 = new Thread(new TestRunnable("thread1"));
    t1.start();
    t1.join();// 會先將 t1.start() 執行完成，才會繼續執行 main()

    System.out.println("main ends");

}
// 印出
// main starts
// t1 start
// t1 running
// t1 ends
// main ends
```

<br/>

<br/>


## 同步 synchronized
1. 多執行緒在使用同一個 class 時，會因為不同步導致資料不一致的狀況。
2. 因為多執行緒的的執行時間無法計算，所以問題不一定會發生，但有機會發生。
3. `Synchronized` 使用時，需指定一個物件，系統會 `Lock` 此`物件`，當程式進入 Synchronized 區塊或 Method 時，該物件會被 Lock，直到離開 Synchronized 區塊時才會被釋放。
4. 在 Lock 期間，鎖定同一物件的其他 Synchronized 區塊，會因為無法取得物件的 Lock 而等待。

<br/>

## 使用 synchronized 的各種用法

`兩種方法都是鎖定(lock) Method 所屬的物件本身。`
1. Synchronized Method
    
    ```java
    synchronized public void syncMethod() {

    }
    ```
2. Synchronized(this)

    ```java
    public void syncMethod() {
        synchronized(SomeObject) {
        
        }
    }
    ```

<br/>

<br/>

## wait(), notify(), notifyAll()
* 3種方法都是 `Object` 所提供的方法，故所有物件都有此方法。
* 必須要在同步化(synchronized)的方法或區塊呼叫。
* wait() 會將物件放進物件等待池(wait pool)中等待，直到時間到或是呼叫了`notify()`，才能進入 `lock pool` 中搶 lock，在 `wait pool` 中是沒有資格搶 lock 的。
