## RabbitMq 安裝/啟動
* 使用 docker 中的 `rabbitmq:management` 開啟在本地端後，會啟動兩個服務: `RabbitMQ服務`、`RabbitMQ web 管理介面`

    ```sh
    docker run --name rabbitmq -d -p 15672:15672 -p 5672:5672 -e RABBITMQ_DEFAULT_USER=root -e RABBITMQ_DEFAULT_PASS=admin1234 rabbitmq:management
    ```

* Port: 
    1. AMQP 中默認的 port 是啟動在 5672
    2. RabbitMQ Web 管理界面是啟動在 15672

<br/>

<br/>

## Springboot 配置

添加依賴
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

配置檔案
```java
@Configuration
public class RabbitMqConfig {

    public static final String TOPIC_EXCHANGE_NAME = "demo-exchange";

    public static final String QUEUE_NAME = "demo-queue";

    public static final String ROUTING_KEY = "demo-routing-key";

    @Bean
    Queue queue() {
        return new Queue(QUEUE_NAME, false);
    }

    @Bean
    TopicExchange exchange() {
        return new TopicExchange(TOPIC_EXCHANGE_NAME);
    }

    @Bean
    Binding binding(Queue queue, TopicExchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with(ROUTING_KEY);
    }

    @Bean
    MessageListenerAdapter listenerAdapter(Receiver receiver) {
        return new MessageListenerAdapter(receiver, "receiveMessage");
    }

}
```

Receiver `@RabbitListener` 用來指定監聽的 Queue，並可以簡單建立一個 Consumer

```java
@Component
public class Receiver {

    @RabbitListener(queues = RabbitMqConfig.QUEUE_NAME) // QUEUE_NAME == "demo-queue"
    public void receiveMessage(String message) {
        System.out.println("Received message:" + message);
    }
}
```

Service 中，使用 RabbitTemplate 來發送訊息給 RabbitMq
```java
@Service
public class MessageService {

    private final RabbitTemplate rabbitTemplate;

    public MessageService(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    public void send(String message) throws Exception {
        System.out.println("Sending message:" + message);
        rabbitTemplate.convertAndSend(
                RabbitMqConfig.TOPIC_EXCHANGE_NAME,
                RabbitMqConfig.ROUTING_KEY,
                message);
    }
}
```

controller
```java
@RestController
public class DemoController {

    @Autowired
    private MessageService messageService;

    @GetMapping("/test/{message}")
    public void test(@PathVariable String message) throws Exception {
        messageService.send(message);
    }

}
```

<br/>

<br/>

## Producer 生產者程式碼

```java
public void producerCode() {

    ConnectionFactory factory = new ConnectionFactory();
    factory.setHost("localhost");
    factory.setUsername("root");
    factory.setPassword("admin1234");

    // 建立 Connection
    Connection connection = factory.newConnection();
    // 從 Connection 中建立 Channel
    Channel channel = connection.createChannel();

    // 用於聲明一個新的消息隊列，或獲取一個已經存在的消息隊列
    // 參數1: Queue名稱
    // 參數2: durable，預設為 False。設置隊列是否持久化，如果設置為 True，當 RabbitMQ 服務器重啟後，該隊列依然存在，否則隊列只存在於當前 RabbitMQ 會話中。
    // 參數3: exclusive，預設為 False，設置是否為獨佔隊列，如果設置為 True，只有該連接可以訪問該隊列，並且在該連接關閉後隊列會被自動刪除。該選項通常用於實現訂閱模式。
    // 參數4: auto_delete，預設為 False。設置隊列是否在沒有任何消費者連接時自動刪除。該選項通常用於臨時隊列。
    // 參數5: arguments，其他的參數，如消息過期時間、隊列最大長度等。該參數是一個字典類型，其中鍵值對的具體含義取決於具體應用場景。

    channel.queueDeclare(QUEUE_NAME, false, false, false, null);

    // 用於將消息發布到指定的交換機中
    // 參數1: exchange，交換器名稱，指定消息要發布到哪個交換器上。
    // 參數2: routing_key，路由鍵，交換器根據該路由鍵將消息發送到相應的隊列中。
    // 參數3: properties，其他的消息屬性，如消息的優先級、過期時間等。該參數是一個字典類型，其中鍵值對的具體含義取決於具體應用場景。
    // 參數4: body，消息主體，二進制類型。需要發送的消息的內容。
    String message = "message";
    channel.basicPublish(null, QUEUE_NAME, null, message.getBytes());
}
```

<br/>

<br/>

## Reference

> 配置檔案 https://matthung0807.blogspot.com/2020/08/spring-boot-rabbitmq-rabbitlistener-simple.html