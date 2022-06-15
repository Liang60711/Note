## Exception 種類

* Checked Exception / Compile Time Exception
* Uncheck Exception / Runtime Exception

## 常見 Exception
* NullPointerException: 為常見的 `Runtime Exception`，若物件為 null 時，再去使用該物件方法時，就會產生。

```java
class User {
    private String name;

    public void setName(String name){
        this.name = name;
    }

    public void getName(){
        return this.name;
    }
}
```
使用 getName().trim() 會報錯 `NullPointerException`
```java
public static void main(String[] args) {
    User user = new User();
    user.getName().trim();  // user.getName() 為 null
}
```

<br/>


<br/>

## 自定義 exception
```java
class MyException extends Exception {
    private String msg;

    // constructor
    public MyException (String msg) {
        this.msg = msg;
    }

    public String getMessage() {
        return this.msg;
    }
}
```
使用
```java
try {
    // 主動 new 一個錯誤
    throw new MyException("error msg");
}catch (MyException e){
    System.out.println(e.getMessage());
}
```

<br/>

<br/>

## 範例
```java
public static void main(String[] args) {

    Scanner input = new Scanner(System.in);
    System.out.println("Enter username");

    try {
        int userName = input.nextInt();// 輸入String會報錯
        System.out.println("Username is: " + userName);
    } catch (InputMismatchException e) {

        e.printStackTrace();
    }

}
```