## IoC / Inversion of Control / 控制反轉
1. 定義: 將物件的控制權交給Spring容器來管理。
2. 好處: 
    * Loose coupling 鬆耦合
    * Lifecycle Management 生命週期管理
    * More testble 方便測試程式

3. 實作方式: 
    * Dependency Injection 依賴注入


<br/>

<br/>

## @Component
1. 用途: 將類變成Spring容器管理的物件。
2. 用法: 只能加在class上。
3. Bean: 被Spring容器所建立的物件統稱為`Bean`，其名稱會是原本的`class名稱字首轉小寫`。

4. 被依賴注入的類需要使用bean時，需要在實作變數(成員變數)上加上`@Autowired`註解，而實作變數盡量使用interface類型。

    ```java
    /**
     * Teacher依賴HpPrinter，需將HpPrinter依賴注入到Teacher中
     */
    @Component
    public class HpPrinter implements Printer{
        
        @Override
        public void print(String message){
            ...
        }
    }

    // 被注入的類也需進入Spring容器中，故加上@Component, @Autowired
    @Component
    public class Teacher{

        @Autowired
        private Printer printer;//@Autowired會找Printer類型的bean來注入
        public void teach(){
            printer.print();
        }
    }
    ```

<br/>

<br/>

## @Qualifier
1. 與`@Autowired`一同使用。
2. 可以指定要注入bean的名字，class的名字字首匯市小寫。

    ```java
    @Autowired
    @Qualifier("bmwCar")
    private Car car;//將bmw這個bean注入給car
    ```

<br/>

<br/>