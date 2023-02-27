## AOP / Aspect Oriented Programming 切面導向程式設計
1. 為了專注於業務邏輯，會將重要但不是業務邏輯的程式碼包裹在業務邏輯的前/後，類似`decorator pattern`的概念。
2. pom.xml 需要加入dependency

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
    </dependency>
    ```

3. AOP應用在:

    * 權限驗證 `Spring Security`
    * 統一的Exception處理 `@ControllerAdvice`
    * Log紀錄


<br/>

<br/>

## @Aspect
1. 加在class上，且需和`@Component`一起使用，也就是這個class必須先成為一個bean，才能成為切面，Annotation沒有順序性。
2. 目的: 宣告這個class是一個切面。

3. 使用`@Before`, `@After`
    ```java
    /**
     * 將此class宣告成切面
     */
    @Aspect
    @Component
    public class MyAspect {
        
        // @Before, @After內的參數稱為"切入點(Pointcut)"
        @Before("execution(* com.example.demo.HpPrinter.*(..))")
        public void before(){
            System.out.println("I am before");
        }

        @After("execution(* com.example.demo.HpPrinter.*(..))")
        public void after(){
            System.out.println("I am after");
        }
    }
    ```

4. 使用`@Around`，在切入點的前+後執行，跟python decorator寫法類似

    ```java
    @Aspect
    @Component
    public class MyAspect {
        
        @Around("execution(* com.example.demo.HpPrinter.*(..))")
        public Object around(ProceedingJoinPoint pjp) throws Thowable{
            System.out.println("I am before");
            Object obj = pjp.proceed(); // 呼叫proceed()，開始執行原方法
            System.out.println("I am after");

            return obj;
        }
    }
    ```
5. 業界目前較少自己寫@Aspect，都是使用第三方library包好的方法。

<br/>

<br/>

## 切入點表達式
> https://code-question.com/developer-blog/spring-aop-pointcut-expression-4

<br/>

<br/>

<br/>

<br/>

