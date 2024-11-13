## 建構3步驟

1. 編寫 Dockerfile 文件。

2. docker build 命令建構 image。

    1. docker從基礎鏡像運行一個容器。

    2. 執行一條指令並對容器做出修改。

    3. 執行類似 docker commit 的操作提交一個新的鏡像層。

    4. docker 再基於剛提交的鏡像運行下一個新容器。

    5. 執行 dockerfile 中的每一筆指令，值到所有指令執行完成。

3. docker run image，依照鏡像運行容器實例。

<br/>

<br/>

## 基礎知識

> 官方文件 https://docs.docker.com/reference/dockerfile/

1. 每個保留字指令必須為大寫字母。
2. 指令從上到下，依序執行。
3. `#` 表示註解。
4. 每條指令都會創建一個新的鏡像層，並對鏡像進行commit。

<br/>

<br/>

## 關鍵字

[可參考](./02_springboot.md#dockerfile)

```dockerfile
# 使用 Ubuntu 作為基礎鏡像
FROM ubuntu:20.04

# 添加 metadata
LABEL maintainer="example@example.com"

# 設置環境變量
ENV APP_HOME=/usr/src/app
ENV NODE_ENV=production

# 建立工作目錄
# 指定在創建容器後，使用終端進入容器時的一個落腳點(docker exec -it 後的目錄位置)。
WORKDIR $APP_HOME

# 指定要用什麼用戶去執行，預設是 root
USER ubuntu:ubuntu

# 先定義掛載點，之後 docker run 再指定宿主主機的路徑即可 -v /host/path:/opt/web/logs
VOLUME [ "/opt/web/upload" ]
VOLUME [ "/opt/web/logs" ]

# RUN 是在建構時的指令。
# 更新並安裝必要的軟件包
RUN apt-get update && apt-get install -y \
    curl \
    gnupg \
    && rm -rf /var/lib/apt/lists/*

# 安裝應用依賴項
RUN curl -sL https://deb.nodesource.com/setup_18.x | bash - && \
    apt-get install -y nodejs && \
    npm install

# ADD 和 COPY 類似，是將宿主機目錄下的文件拷貝進鏡像，且會自動處裡 URL 和解壓縮 tar 包，可以解壓.tar .tar.gz .tar.bz2 但不能解壓 .zip。
# ADD = COPY + 下載 + 解tar包
# 添加外部文件
ADD https://example.com/somefile.tar.gz /tmp/

# 宿主主機目錄下文件 copy 進鏡像
COPY . .

# 暴露應用所需的端口
EXPOSE 8080

# 容器啟動時必須執行的命令
# 類似 CMD，但是不會被 docker run 後面的命令覆蓋，而這些命令行參數會被當作參數給 ENTRYPOINT。
# 如果 ENTRYPOINT 和 CMD 一起使用，則 CMD 會變成 ENTRYPOINT 的參數。
ENTRYPOINT ["java", "-jar"]

# 設置容器啟動時的默認命令或參數，CMD會被 docker run 之後的參數覆蓋掉。
# 當啟動時執行: docker run -it xxxxxx /bin/bash，CMD指令就會被 /bin/bash 給取代掉，就不執行了。
CMD ["app.jar"]

```

補充，當啟動容器時
```sh
docker run -d my-container
```
實際上執行的是
```sh
java -jar app.jar
```

但若加入參數啟動時，原先的CMD參數會被覆蓋掉
```sh
docker run -d my-container another-app.jar
```
實際上會執行的是
```sh
java -jar another-app.jar
```

<br/>

<br/>

## 舉例

使用 centos image，裡面需要安裝 java 和其他的工具。

```Dockerfile
FROM centos
MAINTAINER rico<ricoliang@hpicorp.com.tw>

ENV MYPATH /usr/local
WORKDIR $MYPATH

# centos 已停止維護，故重新指定源
RUN cd /etc/yum.repos.d/ && sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-* && sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-* && cd $MYPATH
# 安裝 vim 編輯器
RUN yum -y install vim
# 安裝 ifconfig
RUN yum -y install net-tools
# 安裝 java 
RUN yum -y install glibc
RUN mkdir /usr/local/java
# Dockerfile 位置和 openjdk 檔案位置需要相同
ADD OpenJDK11U-jdk_x64_linux_hotspot_11.0.25_9.tar.gz /usr/local/java/
# 配置 java 環境變數
ENV JAVA_HOME /usr/local/java/jdk-11.0.25+9
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
ENV PATH $JAVA_HOME/bin:$PATH

EXPOSE 80

CMD echo $MYPATH
CMD echo "Success --------------- ok"
CMD /bin/bash
```

寫好之後，需要輸入以下命令

```sh
# repo名字一定要全部小寫
docker build -t centosjava:11.0.25 .
```