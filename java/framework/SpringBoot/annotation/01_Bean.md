## 查看 Bean
```java
public static void main(args[] String){
    run.containsBean("beanName");//查看bean，返回boolean
}
```

<br/>

<br/>

## @RestController, @Service, @Repository
此3個為常用的註解，都屬於 @Component

<br/>

<br/>


## @Import
不常用，可做為 @Configuration修飾的類
```java
@Configuration(proxyBeanMethod = false) // lite模式
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