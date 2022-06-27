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

4. 被依賴注入的類需要使用bean時，需要在實作變數(成員變數)上加上`@Autowired`註解，而實作變數盡量使用interface類型，`@Autowired`會根據變數的類型，去 Spring 容器中尋找有沒有符合類型的 Bean。

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
2. 可以指定要注入bean的名字，class的名字字首會是小寫。

    ```java
    @Autowired
    @Qualifier("bmwCar")
    private Car car;//將bmw這個bean注入給car
    ```

<br/>

<br/>

## @Configuration / @Bean

`@Configuration`
1. 只能加在class上。
2. 將此類變成設定Spring用的class。


`@Bean`  

1. 只能加在帶有 @Configuration class 的`方法`上，將返回的物件放入bean。
2. 目的是在Spring容器中建立一個Bean。
3. `Bean()` 中可以自訂bean的名稱，如果沒填寫預設是`方法名稱`。

    ```java
    @Configuration
    public class MyConfiguration{

        @Bean("beanName")
        public Printer myPrinter() {
            return new HpPrinter();
        }
    }
    ```

4. @Bean與@Component可以混用，都會在容器中建立Bean。


<br/>

<br/>

## @PostConstruct 與 InitializingBean afterPropertiesSet()
1. 可以初始化Bean的方式有這2種

2. `@PostConstruct` 使用方式

    * 必須是public void 方法，不得有返回值，不得有參數
    * 方法名字沒綁定可以隨意取

        ```java
        @Component
        public class HpPrinter implements Printer{

            private int count;

            @PostConstruct
            public void init(){
                count = 5;
            }
        }
        ```

3. 實作`InitializingBean`這個interface

    ```java
    @Component
    public class HpPrinter implements Printer, InitializingBean {

        private int count;

        // 實作這個方法，語法比較多，多半還是會使用@PostConstruct
        @Override
        public void afterPropertiesSet() throws Exception {
            count = 5;
        }
    }
    ```


4. 類的初始化執行順序如下:

    `Constructor >> Autowired >> PostConstruct`

    故，如果初始化成員變數需要使用注入進來的物件或者值，那麼應該放在被PostConstruct註解的方法中去做。


<br/>

<br/>

## Bean的生命週期
1. 啟動Spring Boot
2. 建立Bean1 -> 初始化Bean1 -> 建立Bean2 -> 初始化Bean2，直到所有Bean被建立和初始化。
3. 建立完成後會在console顯示 `Started ... in ... Seconds`，表示運行成功。


* 重點: 
    1. 當所有Bean都可以被使用後，Spring Boot才會運行成功(只要有一個失敗就不行)。

    2. 當有多個Bean之間有依賴關係時，容器會依照bean之間的依賴關係決定bean創建的順序(被依賴會先創建)。

    3. 需要避免寫出`循環依賴`的程式，否則會無法決定bean的建立順序。

<br/>

<br/>