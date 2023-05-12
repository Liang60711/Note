## RabbitMq 啟動
* 使用 docker 中的 `rabbitmq:management` 開啟在本地端後，會啟動兩個服務: `RabbitMQ服務`、`RabbitMQ web 管理介面`

    ```sh
    docker run --name rabbitmq -d -p 15672:15672 -p 5672:5672 \
    -e RABBITMQ_DEFAULT_USER=root -e RABBITMQ_DEFAULT_PASS=admin1234 \
    rabbitmq:management
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

## Reference

> 配置檔案 https://matthung0807.blogspot.com/2020/08/spring-boot-rabbitmq-rabbitlistener-simple.html