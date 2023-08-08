## 重點
1. 屬於OSI模型中的應用層，基於傳輸層的TCP協議。
2. 此協議是`瀏覽器`和`伺服器`交換資料的協議。
3. 與 HTTP 最大的不同是，只需連線一次，就能保持雙向溝通，不需要重複發送 Request，提升通訊速度。
4. 此協議允許伺服器向瀏覽器端推播資料，有效解決HTTP中`輪詢(Polling)`產生的效能問題。
5. WebSocket 是建立在 HTTP 協議之上的一種協議，它使用了 HTTP 協議的握手協議來建立連接。在 WebSocket 握手成功之後，客戶端和服務器之間的通信將不再遵循 HTTP 協議的規則，而是採用 WebSocket 自己的數據傳輸協議


<br/>

<br/>

## 實作
websocket 的建立是透過 javascript，發起一個 HTTP request，並包含一個特殊的 header，該 header 表示客戶端希望升級協議到 websocket，如果該伺服器支援 websocket，則在響應時，也會再包含此 header。

```sh
# no ssl
ws://example.com/wsapi
# ssl
wss://example.com/wsapi
```

WebSocket 進行一次雙方握手過程，Client 端發送資訊：

```
GET ws://localhost:8080 HTTP/1.1             // 透過 HTTP/1.1 進行交握，一定要使用GET
Origin: http://localhost:8080                // Client 端 URL
Host: localhost:8080
Upgrade: websocket                           // 表示發送 WebSocket 類型請求
Connection: Upgrade                          // 表示 Client 希望連接升級
Sec-WebSocket-Key: xqBt3ImNzJbYqRINxEFlkg==  // base64 編碼密文，瀏覽器在每次握手隨機生成
Sec-WebSocket-Protocol: chat, superchat      // 用來區分同 URL 下不同 Server 需要之協議
Sec-WebSocket-Version: 13                    // 支持的 WebSocket 版本號
```

<br/>

<br/>

## STOMP 協議
STOMP (Simple Text Orientated Messaging Protocol) 是一種基於 TCP 的簡單訊息傳輸協定，後來 Websocket 出現後，STOMP 也可以基於 Websocket 來傳輸。

STOMP 用於在 WebSocket 等傳輸協議之上實現異步消息傳遞。

以下為 SpringBoot 設定

1. 添加依賴

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-websocket</artifactId>
    </dependency>
    ```

2. 建立 Configuration 配置檔案，需繼承 `AbstractWebSocketMessageBrokerConfigurer`

    ```java
    @Configuration
    @EnableWebSocketMessageBroker // 允許啟用訊息代理(message broker)
    public class WebSocketBrokerConfig extends AbstractWebSocketMessageBrokerConfigurer {
        
        /**
         * 註冊 WebSocket EndPoint
         */
        @Override
        public void registerStompEndpoints(StompEndpointRegistry registry) {
            registry.addEndpoint("/eboard").setAllowedOrigins("*");              // 前端使用StompJS連線(Websocket)
            registry.addEndpoint("/eboard").setAllowedOrigins("*").withSockJS(); // 前端使用SockJS連線(SockJS為原生JS庫，因為有些瀏覽器不支援Websocket，故使用此原生套件來模擬Websocket，本身並非使用Websocket)
        }

        /**
         * 用來定義訊息代理(broker)的配置
         */
        @Override
        public void configureMessageBroker(MessageBrokerRegistry config) {
            config.enableSimpleBroker("/topic1", "/topic2");  // 建立destination (websocket連線後，需訂閱此destination才能收到訊息，意義類似topic)
            config.setApplicationDestinationPrefixes("/app"); // 前端傳送訊息給broker時需要加的前綴
        }
    }
    ```
3. 後端 broker 發送訊息給前端
    
    ```java
    // 後端
    public abstract class MqttPayloadProcessor {
        
        @Autowired
	    protected SimpMessagingTemplate broker;

        public void sendMessage() throws Exception {
            broker.convertAndSend("/topic1", "Hello World"); // 向主題發送訊息
        }
    }
    ```

    ```javascript
    // 前端
    var deviceResourceClient;

    deviceResourceClient = Stomp.client(endPointURL);
    deviceResourceClient.connect({}, function (frame) {
        var subscribeUrl = '/device/resource';
        deviceResourceClient.subscribe(subscribeUrl, function (response) {
            var responseBody = JSON.parse(response.body);
            if(responseBody && deviceId === responseBody.device_id){
                setDeviceResource(responseBody);
            }
        })
    });
    ```

4. 前端發送訊息給後端broker
    ```javascript
    // 前端
    var stompClient = null;

    // 連線順便訂閱
    function connect() {
        var socket = new SockJS('/Test'); //建立一個socket物件 名稱為: /Test
        stompClient = Stomp.over(socket);
        stompClient.connect({}, function (frame) {
            setConnected(true);
            console.log('Connected: ' + frame);
            stompClient.subscribe('/topic/getResponse', function (response) {
                console.log(response)
                showConversation(JSON.parse(response.body).responseMessage); //
            });
        });
    }

    // 傳送訊息方法
    function sendName() {
        var name = $("#name").val();
        stompClient.send("/messageControl", {}, JSON.stringify({'name': name}));
    }
    ```

    ```java
    // 後端
    @Controller
    public class WebsocketController {

        @MessageMapping("/hello")   // 需要去除前綴 /app
        @SendTo("/topic/greetings") // 在此方法處理訊息後，並轉發給前端主題(可寫可不寫)
        public Greeting greeting(MyMessage message) throws Exception { // 可以使用物件來接前端傳的 JSON String

            Thread.sleep(1000); // simulated delay

            return new Greeting("Hello, " + message.getName());
        }
    }
    ```


<br/>

<br/>

## 參考資料

> 解釋 https://hackmd.io/@Heidi-Liu/javascript-websocket#11-What-is-WebSocket

> 實作範例 https://docs.flydean.com/spring-framework-documentation5/webservlet/4.websockets/4.4stomp