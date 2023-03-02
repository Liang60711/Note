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

## 可以生成 Bean 的註解
1. @Configuration (full或lite模式)
2. @Bean, @Component, @Controller, @Service, @Repository
3. @ComponentScan, @Import
4. @Conditional

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

5. `proxyBeanMethods`參數，

    * `Lite模式`下，不會檢查Bean之間的依賴關係，每次從IOC容器取得的Bean都不一樣，是一個新的物件 (所以就不是Singleton了)，所以可以加速容器啟動過程，減少IOC容器的資源。
    * `Full模式`下，Bean之間有依賴關係，會檢查依賴，每次獲取物件都是從IOC容器中取，並確保單例模式。

    ```java
    // 預設是Full模式 - true
    // 可改成Lite模式 - false
    @Configuration(proxyBeanMethods = false)
    public class MyConfig {
    }
    ```

<br/>

<br/>


## @Import
可做為 @Configuration修飾的類
```java
@Configuration(proxyBeanMethods = false) // lite模式
@Import({
    XXXDataConfiguration1.class,
    XXXDataConfiguration2.class
})
public class XXXXXXDataConfiguration0 {
    //
}
```

<br/>

<br/>

## @Condition
在某條件下建立Bean
```java
// 當有名為 Person  的 Bean，才去建立 User Bean
@ConditionOnBean(name = "Person")
public class User{

}

// 與上相反，沒有 Person Bean 時，才去建立 User Bean
@ConditionOnMissingBean(name = "Person")
public class User {

}
```

<br/>

<br/>

## @Import Resource
老專案會使用 xml 檔案來配置 bean，就可以用此方法導入
```java
// 檔案路徑為src/main/Resources/beans.xml
@ImportResource("classpath:beans.xml")
public class MyConfig {

}
```

<br>

<br/>

## @PostConstruct 與 InitializingBean afterPropertiesSet()
可以初始化Bean的方式有這2種

1. `@PostConstruct` 使用方式

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

2. 實作`InitializingBean`這個interface

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


* 類的初始化執行順序如下:

    * `Constructor >> Autowired >> PostConstruct`

* 使用 PostConstruct 取代 Constructor 的原因

    * 依賴注入(Autowired)尚未完成，故使用 PostConstruct 取代 Constructor，確保該載入的依賴都被載入後才初始化。
    * PostConstruct 可確保初始化只會被呼叫一次。

    ```java
    /**
     * 承襲上述，Bean 只會被實例化一次
     */

    public static void main(String[] args) {
        
        // 1. 可以返回IOC容器
        ConfigurableApplicationContext run = SpringApplication.run(JspApplication.class, args);
        
        // 2. 查看裡面的組件
        String[] names = run.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println(name);
        }
        
        // 3. 從容器中獲取組件
        User user01 = run.getBean("name", User.class);
        User user02 = run.getBean("name", User.class);

        System.out.println(user01 == user02); // true，因為Bean是單例模式
    }
    ```

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

## BeanFactory 和 ApplicaionContext
兩個類都是spring容器所提供的 interface，用來操作、檢查、獲取 bean

||BeanFactory|ApplicationContext|
|--|:--:|:--:|
|Annotation support|No|Yes|
|BeanPostProcessor Registration|Manual|Automatic|
|Implementation|XMLBeanFactory|ClassPath/FileSystem/WebXmlApplicationContext|
|Internationalization|No|Yes|
|Enterprise services|No|Yes|
|ApplicationEvent publication|No|Yes|
|載入方式|Lazy|Eager|