## 用途
用來加密 yml 中的密碼字串，並使用 `ENC()` 作為標記，在springboot啟動載入密碼時會自動解密。

進入 maven repo

`cd .m2/repository/org/jasypt/jasypt/1.9.3`

加密指令

`java -cp jasypt-1.9.3.jar org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI input="root" password=test algorithm=PBEWithMD5AndDES`


解密指令

`java -cp jasypt-1.9.3.jar org.jasypt.intf.cli.JasyptPBEStringDecryptionCLI input="xxxxxxxxxxxxxx" password=test algorithm=PBEWithMD5AndDES`

<br/>

<br/>

## 步驟
1. 加入依賴

    ```xml
    <dependency>
        <groupId>com.github.ulisesbocchio</groupId>
        <artifactId>jasypt-spring-boot-starter</artifactId>
        <version>3.0.3</version>
    </dependency>
    ```

2. 在 yml 中加入 jasypt 配置

    ```yml
    jasypt:
      encryptor:
        password: test
        # version 3.0.0 之後需要加上下面配置
        algorithm: PBEWithMD5AndDES
        iv-generator-classname: org.jasypt.iv.NoIvGenerator
    ```


3. 使用 jasypt CLI 工具取得加密後文字

    * 進入 .m2 目錄 

        ```sh
        # 版本自行更換
        cd .m2/repository/org/jasypt/jasypt/1.9.3
        ```

    * 指令目的是將 input 的字串進行加密，此 input 就是在 yml 中需要保護的密碼

        ```sh
        # input => 需要加密的文字
        # password => 加密密鑰
        # algorithm => 加密演算法

        java -cp jasypt-1.9.3.jar org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI input="root" password=test algorithm=PBEWithMD5AndDES
        ```

    * 輸入以下可列出所有演算法

        ```sh
        java -cp jasypt-1.9.3.jar org.jasypt.intf.cli.AlgorithmRegistryCLI
        ```
    
    * 輸入後會產生以下結果，輸出結果因為隨機鹽，每次都不同

        ```sh
        ----ENVIRONMENT-----------------

        Runtime: Oracle Corporation Java HotSpot(TM) 64-Bit Server VM 25.121-b13

        ----ARGUMENTS-------------------

        algorithm: PBEWithMD5AndDES
        input: root
        password: test

        ----OUTPUT----------------------

        zNOr7ReOk1IT55eUPlEutw==
        ```


4. 將 yml 中的密碼加上 `ENC()`，這樣jasypt就會對此字串自動加密解密

    ```yml
    spring:
      datasource:
        url: jdbc:mysql://localhost:3306/db?useSSL=false&characterEncoding=UTF-8
        username: root
        password: ENC(qfVLCII/YydI9mgoJ5fnxw==)
    ```