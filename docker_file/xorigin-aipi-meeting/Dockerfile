FROM 172.16.50.75:8084/library/ubuntu:24.04 AS builder

# 避免交互式安装
ARG DEBIAN_FRONTEND=noninteractive
# 在容器根目录 创建一个 apps 目录
WORKDIR /app

COPY ./build/ca-certificates_20250419_all.deb /app/

RUN apt-get update && apt-get install -y openssl

RUN rm -f /etc/apt/sources.list.d/ubuntu.sources

COPY ./build/ubuntu.list /etc/apt/sources.list.d/ubuntu.list


RUN dpkg -i /app/ca-certificates_20250419_all.deb && \
        apt-get update && \
        apt-get install -f -y

# 安装必要的软件包，包括 ca-certificates 和 wget（示例）
RUN apt-get update && \
    apt-get install -y --no-install-recommends libopus0 ffmpeg && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# 运行镜像，直接采用jre镜像
FROM ubuntu/jre:21-24.04_stable
WORKDIR /app

# 复制ffmpeg和libopus
COPY --from=builder /usr/bin/ffmpeg /usr/bin/
COPY --from=builder /usr/lib/x86_64-linux-gnu/libopus.so.0 /usr/lib/x86_64-linux-gnu/

# 创建日志目录
RUN mkdir /logs/xorigin-aipi-meeting

# 将构建好的JAR文件复制到镜像中
ADD ../target/xorigin-aipi-meeting.jar ./app.jar

# 设置编码
ENV LANG=C.UTF-8

EXPOSE 8190

ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-XX:+HeapDumpOnOutOfMemoryError","-XX:+UseZGC","-jar","app.jar"]
