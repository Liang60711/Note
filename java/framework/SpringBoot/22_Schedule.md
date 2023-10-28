## @Schedule 配置

```java
@ConditionalOnProperty(
        name = "customize.schedule.xxx.enable",
        havingValue = "true")
@Configuration
@EnableScheduling
public class XXXSchedule {

    @Scheduled(cron = "0 0/10 * * * ?")
    public void refreshStatus() throws Exception {
        // ...
    }
}
```

<br/>

<br/>

## @Schedule 異步配置
Springboot 中的 @Schedule 預設會使用一個線程來處理所有的排程，故一個排程若處理太久，會導致後續的排程阻塞(等待的任務會加入到queue中)，因為 Springboot 預設的 Scheduler 線程池大小為 1，算是一個坑，可能是因為開新線程的資源消費巨大。

### 解法1: 使用 yml 配置

```yml
spring:
  task:
    scheduling:
      pool:
        size: 1 # 任務線程池大小預設為1，可在此調整
```

<br/>

<br/>

### 解法2: 使用配置類

使用 @Async 註解，會將此註解方法使用新的線程執行，不會阻塞線程池的主線程。

```java
// 第1步
@EnableAsync    // 啟動類設置異步
@SpringBootApplication
public class XXXApplication {
    public static void main(String[] args) {
        SpringApplication.run(XXXApplication.class, args);
    }
}
```

```java
// 第2步，配置線程池(這裡不設定也行，springboot 會有預設值)
@Configuration
@EnableAsync
public class AsyncConfig {

    public static final String ASYNC_EXECUTOR = "asyncExecutor";

    @Bean(name = ASYNC_EXECUTOR)
    public Executor asyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(20);
        executor.setMaxPoolSize(20);    
        executor.setKeepAliveSeconds(120);  // 設置線程活躍時間（秒）
        executor.setQueueCapacity(100);     // 設置Queue容量
        executor.setThreadNamePrefix("AsyncThread-");
        executor.setWaitForTasksToCompleteOnShutdown(true); // 等待所有任務结束後再關閉線程池
        executor.initialize();
        return executor;
    }
}
```

```java
// 第3步，使用@Async
@Async(value = "asyncExecutor")
@Scheduled(cron = "0 0 0/1 * * ? ")
public void task1() {
    // ...
}
```

<br/>

<br/>

## @Async
一般使用情境，可以使用在 @Schedule 上，也可以使用在非排程上，較耗時，且不用管返回結果的方法上。

```java
public void AsyncTask(){

    @Async
    public void doSomething1(int sleepTime) {
        try {
            Thread.sleep(sleepTime);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    
    @Async
    public Future<String> doSomething2() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return null;
    }

 }
```

但這裡要注意的是，`@Async若要生效，只能使用 @Bean調用`，若使用內部調用，則會無效，[這邊跟@Cachable相同](21_Cache.md#特別需要注意)