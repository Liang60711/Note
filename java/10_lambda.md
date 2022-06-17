## Lambda
* 定義函式介面實作的一種方式
* 不用建立實作的類別
* 不用輸入方法名稱
* 用法: 箭頭 `->` 前輸入引數，箭頭後輸入邏輯，與箭頭函數用法相同。
* 優點: 
    1. 讓程式碼更簡潔。
    2. 內部類會產生編譯文件(Outer`$`Inner.class)，lambda不會產生編譯文件。

<br/>

舉例1: 實作interface有三種寫法
```java
public class Test{
    public static void main(String[] args){
        // 第一種，實作接口方法
        IEatImple ieat1 = new IEatImple();
        ieat1.eat();


        // 第二種，接口式匿名內部類
        IEat ieat2 = new IEat(){
            public void eat(){
                System.out.print("eat");
            }
        };

        // 第三種，lambda(限制是interface中只能有一個方法，若IEat有兩個未實作方法，則無法使用)
        IEat ieat3 = () -> {System.out.print("eat");};
    }
}

```
```java
interface IEat{
    public void eat();
}

// 第一種，implements
class IEatImple implements IEat{
    public void eat(){
        System.out.print("eat");
    }
}
```

<br/>

<br/>

舉例2: 帶參數
```java
interface IEat{
    public void eat(String food);
}
```
```java
// 帶參lambda
// 1. 參數可以不用寫(String food)，可省略String
// 2. 若只有一行，可省略大括號
IEat ieat = (food) -> System.out.print("eat "+food);
ieat.eat("pizza");
```
如果是有返回值且只有一行，則可以不用加return
```java
interface IEat{
    public int eat(String food);
}
```
```java
IEat ieat = (food) -> 10; 
IEat ieat = (food) ->{return 10;}; //要加return就要加大括號
```

<br/>

<br/>

舉例3:
```java
// Comparator 為介面，compare，compare() 為未實作方法
Comparator<Integer> com = (x, y) -> x-y;
int ret = com.compare(5,2);
System.out.println(ret);
```
可以在 `ArrayList.forEach()` 使用 
```java
List<String> list = new ArrayList<String>();
list.add("A");
list.add("B");
list.add("C");

// lambda 函式與 callback 用法相同
list.forEach(item -> System.out.println(name);)
```
可以使用靜態方法(static method)來定義函式介面實作，但輸入與回傳參數需一致，並使用 `::` 來使用。
```java
// 相減的靜態方法
Math::subtractExact(5, 2);

// 可以參考以上方法，來實作
Comparator<Integer> com = Math::subtractExact; // 這邊方法不用加小括號

Integer result = com.compare(5, 2);
```