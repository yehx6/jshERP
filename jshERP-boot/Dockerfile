# 阶段1: Maven构建
FROM maven:3.6.3-jdk-8 AS builder

WORKDIR /build

# 先复制pom.xml，利用Docker缓存加速依赖下载
COPY pom.xml .

# 配置Maven阿里云镜像并下载依赖
RUN mkdir -p /root/.m2 && \
    echo '<?xml version="1.0" encoding="UTF-8"?> \
    <settings xmlns="http://maven.apache.org/settings/1.0.0" \
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" \
              xsi:schemaLocation="http://maven.apache.org/settings/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd"> \
        <mirrors> \
            <mirror> \
                <id>aliyunmaven</id> \
                <mirrorOf>*</mirrorOf> \
                <name>阿里云公共仓库</name> \
                <url>https://maven.aliyun.com/repository/public</url> \
            </mirror> \
        </mirrors> \
    </settings>' > /root/.m2/settings.xml && \
    mvn dependency:go-offline -B

# 复制源码并构建
COPY src ./src
RUN mvn clean package -DskipTests -B

# 阶段2: 运行
FROM openjdk:8-jre-slim

WORKDIR /app

# 设置时区为中国
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 创建上传目录和日志目录
RUN mkdir -p /opt/jshERP/upload /opt/tmp/tomcat /logs

COPY --from=builder /build/target/jshERP.jar ./jshERP.jar

EXPOSE 9999

ENTRYPOINT ["java", "-jar", "jshERP.jar"]
