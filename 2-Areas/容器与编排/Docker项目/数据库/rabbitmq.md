# RabbitMQ 消息队列

开源的消息代理软件，支持 AMQP 协议，提供可靠的消息传递、路由和队列管理功能。此处为三节点集群部署。

## 部署

```yaml
version: "3.9"
services:
  rabbitmq1:
    image: rabbitmq:3-management
    hostname: rabbitmq1
    container_name: rabbitmq1
    networks:
      rabbitmq_net:
        ipv4_address: 172.21.0.2
    ports:
      - 15672:15672
      - 5675:5672
    environment:
      RABBITMQ_DEFAULT_USER: admin
      RABBITMQ_DEFAULT_PASS: Admin@123
      RABBITMQ_ERLANG_COOKIE: rabbitcookie
    restart: always
    volumes:
      - ./rabbitmq1/data:/var/lib/rabbitmq/mnesia
      - /etc/localtime:/etc/localtime:ro
  rabbitmq2:
    image: rabbitmq:3-management
    hostname: rabbitmq2
    container_name: rabbitmq2
    networks:
      rabbitmq_net:
        ipv4_address: 172.21.0.3
    ports:
      - 5673:5672
    environment:
      RABBITMQ_ERLANG_COOKIE: rabbitcookie
    restart: always
    volumes:
      - ./rabbitmq2/data:/var/lib/rabbitmq/mnesia
      - /etc/localtime:/etc/localtime:ro
  rabbitmq3:
    image: rabbitmq:3-management
    container_name: rabbitmq3
    hostname: rabbitmq3
    networks:
      rabbitmq_net:
        ipv4_address: 172.21.0.4
    ports:
      - 5674:5672
    environment:
      RABBITMQ_ERLANG_COOKIE: rabbitcookie
    restart: always
    volumes:
      - ./rabbitmq3/data:/var/lib/rabbitmq/mnesia
      - /etc/localtime:/etc/localtime:ro
  # haproxy-service:
  #   image: haproxy:1.7
  #   container_name: haproxy-service
  #   networks:
  #     rabbitmq_net:
  #       ipv4_address: 172.21.0.5
  #   ports:
  #     - 5672:5672
  #   volumes:
  #     - ./haproxy_service/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg
  #     - /etc/localtime:/etc/localtime:ro
  #   restart: always
networks:
  rabbitmq_net:
    driver: bridge
    ipam:
      config:
        - subnet: 172.21.0.0/16
```

## 说明

- 管理界面（rabbitmq1）：`http://<IP>:15672`
- AMQP 端口：`5675`（rabbitmq1）、`5673`（rabbitmq2）、`5674`（rabbitmq3）
- `RABBITMQ_DEFAULT_USER` / `RABBITMQ_DEFAULT_PASS`：管理界面登录凭据（仅 rabbitmq1）
- `RABBITMQ_ERLANG_COOKIE`：集群节点间通信的 Erlang Cookie，三个节点必须一致
- 集群网络：`rabbitmq_net`（子网 `172.21.0.0/16`）
  - rabbitmq1：`172.21.0.2`
  - rabbitmq2：`172.21.0.3`
  - rabbitmq3：`172.21.0.4`
- 数据持久化：各节点数据分别挂载到 `./rabbitmq1/data`、`./rabbitmq2/data`、`./rabbitmq3/data`
