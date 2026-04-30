# Docker 部署 Kafka 可视化管理工具

使用 AKHQ 或 kafka-manager 通过 Docker 部署 Kafka 集群可视化管理界面。

## AKHQ（推荐，支持 KRaft 模式）

下载地址：https://github.com/tchiotludo/akhq/releases

```yaml
version: "3.8"
services:
  akhq:
    image: tchiotludo/akhq:0.25.1
    container_name: akhq
    hostname: akhq
    environment:
      AKHQ_CONFIGURATION: |
        akhq:
          connections:
            my-cluster:
              properties:
                bootstrap.servers: "kafka01:9092"
    ports:
      - 5002:8080
    extra_hosts:
      - "kafka01:192.168.100.150"
      - "kafka02:192.168.100.151"
      - "kafka03:192.168.100.152"
```

## kafka-manager（基于 ZooKeeper）

```yaml
version: "3.8"
services:
  kafka-manager:
    image: sheepkiller/kafka-manager
    container_name: kafka-manager
    environment:
      ZK_HOSTS: zookeeper:2181
    ports:
      - "9001:9000"
    networks:
      - kafka-net
networks:
  kafka-net:
```

## 说明

| 工具 | 特点 | 端口 |
| --- | --- | --- |
| AKHQ | 支持 KRaft 模式，功能全面 | `5002:8080` |
| kafka-manager | 基于 ZooKeeper，轻量 | `9001:9000` |
