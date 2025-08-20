# Springboot 應用程式使用 docker 佈署

## Dockerfile
`FROM`  
* 指定了基礎映像（base image），Docker image 將基於這個基礎映像進行構建。

* 多階段構建中，可以使用多個 FROM 指令來優化最終映像。

* Docker 支持多階段構建，表示可以在 Dockerfile 中定義多個 FROM 階段。
好處是可以在不同階段使用不同的映像來執行不同的任務，並在最終映像中僅保留必要的部分，以下方為例，最終只會保留最後的 Image。


```dockerfile
# build

# base image
FROM maven:3.8.5-openjdk-11 AS build
# 工作目錄，可以一併將 application.yml 複製到這
WORKDIR /app
# 從專案目錄(本地)，複製到Image中的 WORKDIR (/app)
COPY . .
RUN mvn clean package -DskipTests

# prod

# base image
FROM adoptopenjdk/openjdk11:alpine-jre
# 工作目錄，跟上一個maven的基礎鏡像是不同環境
WORKDIR /app
# build 指 FROM maven:3.8.5-openjdk-11 AS build 的這個環境
COPY --from=build /app/target/*.jar web.jar
# 暴露 8080 port
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "-Xms2048m", "-Xms2048m", "/app/web.jar"]
STOPSIGNAL SIGQUIT
```

build image 指令

```sh
# 依照 dockerfile 中的配置，建構 image
# -t 為 tag，類似 alias，將 image 命名為 my-springboot-app
# . 為當前目錄
docker build -t my-springboot-app .
```

run 指令
```sh
docker run -p 8080:8080 my-springboot-app
```
