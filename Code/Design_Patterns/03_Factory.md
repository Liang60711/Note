## 簡單工廠模式
是一種管理物件創建的模式，隨著輸入的參數不同，簡單工廠會回傳不同的物件，使用者取得物件的時候只要傳入正確的參數，不需要去理解這個物件。

<br/>

<br/>

程式
```java
// 輸入產品參數，回傳不同的產品
interface Product{
    public void work();
}

class Phone implements Product{
    public void work(){
        System.out.println("phone works");
    }
}

class Computer implements Product{
    public void work(){
        System.out.println("computer works");
    }
}

// 建立工廠類
class ProductFactory{
    public Product getProduct(String name){
        if("phone".equals(name)){
            return new Phone();
        }else if("computer".equals(name)){
            return new Computer();
        }else{
            return null;
        }
    }
}
```
使用
```java
public static void main(String[] args){
    Product phone = ProductFactory.getProduct("phone");
    if(phone != null){
        phone.work();
    }
}
```