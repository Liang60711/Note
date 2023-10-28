## 發送Email
1. 加入依賴

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-mail</artifactId>
    </dependency>
    ```


2. 到 google 帳戶設置應用程式密碼(安全性 >> 兩步驟驗證 >> 拉到最底下，應用程式密碼)，建立後會產生一組16個字串的密碼。



3. 配置發送者資訊

    ```yml
    spring:
        mail:
            host: smtp.gmail.com  # 郵件供應商
            username: liang60711@gmail.com  # 寄件人帳戶
            password: xxxxxxxxxxxxxxxx # 非帳戶密碼，而是 gmail 向用戶提供的應用程式密碼(16位)
            port: 587
            properties:
                mail:
                    smtp:
                        starttls:
                            enable: true    # 這個配置是因應 google 安全性
    ```

4. 程式碼

    ```java
    @Autowired
    private JavaMailSender javaMailSender;

    public void sendMail() {
        // SimpleMailMessage 需要設定4個資訊
        SimpleMailMessage message = new SimpleMailMessage();
        message.setFrom("liang60711@gmail.com");// 1.發送人
        message.setTo("liang60711@gmail.com");  // 2.接收人
        message.setSubject("測試主旨");          // 3.主旨
        message.setText("測試內文");             // 4.內文
        javaMailSender.send(message);
    }
    ```