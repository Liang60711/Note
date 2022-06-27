## pom.xml 基本結構
```java
<?xml version="1.0" encoding="UTF-8"?>

// 以xsd檔案設定pom.xml的語法標準，統一撰寫標準
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    // 模組版本，在maven2, 3 都是4.0.0
    <modelVersion>4.0.0</modelVersion>

    // 識別管理，分三層
    // G:企業標示 A:可以使用專案名稱 V:版本
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    
    // 依賴列表
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

<br/>

<br/>

## 管理流程
1. Maven下載的jar檔會儲存在本地倉庫。
2. 本地不同專案可以共用`本地倉庫`中的jar檔案。
3. 設定本地倉庫路徑的檔案位置: `%MAVEN_HOME%\conf\settings.xml` 中的 `localRepository`

    ```xml
    <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 
    http://maven.apache.org/xsd/settings-1.0.0.xsd">
        <localRepository>C:/MyLocalRepository</localRepository>
    </settings>
    ```