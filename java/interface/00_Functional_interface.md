# JDK8 新特性
新的interface 都放在`java.util.function`，大致分為4類。

> https://matthung0807.blogspot.com/2018/09/java-8-javautilfunction-functional.html

## Consumer<T>
* 屬於`Functional Interface`，意思是只有一個抽象方法的interface。
* Consumer class下只有一個 `accept`方法。

    ```java
    @FunctionalInterface
    public interface Consumer<T> {
        void accept(T t);
    }
    ```
* 接受一個參數，然後處理後不回傳值，這行為就像純綷消耗了參數，也就是命名為Consumer的原因。
* Iterator介面中的forEach()方法中就是使用Consumer作為參數。


<br/>

<br/>



## function<T, R>
* 接受一個參數，然後以該參數進行計算後傳回結果，就可以使用Function介面。

* 此介面就像是數學函數y=f(x)，給予x值計算出y值的概念，因此命名為Function，應用的例子之一

* 泛型中的T代表傳入類型，R代表回傳類型。

    ```java
    @FunctionalInterface
    public interface Function<T, R> {
        R apply(T t);
    }
    ```

* `Optional.map(Function<? super T,? extends U> mapper)`即以Function<T,R>為參數。


<br/>

<br/>

## Predicate<T>
* 接受一個參數，並回傳`boolean`值，根據傳入的參數直接斷定真假的行為。

    ```java
    @FunctionalInterface
    public interface Predicate<T> {
        boolean test(T t);
    }
    ```

<br/>

<br/>

## Supplier<T>
* 不接受任何引數，然後傳回值，就可以使用Supplier介面。
* Supplier<T>的get()用來回傳特定的結果，回傳值的型態須同呼叫的方法的回傳型態。

    ```java
    @FunctionalInterface
    public interface Supplier<T> {
        T get();
    }
    ```
* 舉例

    ```java
    public static void main(String[] args){
         supplierTest();
    }

    // 獲取list並打印
    private static void supplierTest(){
        List<Integer> list = getNum(10, ()->{return (int) (Math.random()*100);});//因為Supplier只有get()一個方法，所以lambda是override get()這個方法
        list.forEach(System.out::println);
    }

    private static List<Integer> getNum(int num, Supplier<Integer> sup){
        List<Integer> list = new ArrayList<>();

        for(int i=0; i<num; i++){
            list.add(sup.get());//呼叫lambda，並將返回值加入list
        }

        return list;
    }
    ```



<br/>

<br/>

## Functional Interface 比較


|interface名稱|是否傳入參數|是否回傳/回傳類型|方法名稱|
|--|:--:|--|--|
|Consumer|是|否|accept()|
|Function|是|是|apply()|
|Predicate|是|是/boolean|test()|
|Supplier|否|是|get()|