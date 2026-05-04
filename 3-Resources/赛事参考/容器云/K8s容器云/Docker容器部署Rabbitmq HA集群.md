# Docker容器部署Rabbitmq HA集群

Docker容器部署Rabbitmq HA集群相关技术笔记。

使用 Docker 容器化部署。

**Rabbitmq HA**

在master节点上编写/root/rabbitmq-cluster/docker-compose.yml文件编排部署RabbitMQ HA集群，该集群包含三个节点，三个节点的服务名称和容器名称分别为rabbitmq-node-1、rabbitmq-node-2和rabbitmq-node-3，启用RabbitMQ Management，用户名为admin，密码为Admin@123。

完成后编排部署RabbitMQ集群，并提交master节点的用户名、密码和IP到答题框。（需要用到的Docker镜像包路径[http://10.24.1.46/Rabbitmq-cluster.tar](http://10.24.1.46/Rabbitmq-cluster.tar)）

1.上传镜像

[root@k8s-master-node1 ~]# docker load -i Rabbitmq-cluster.tar

2.创建rabbitmq-cluster目录下编排docker-compose.yaml并启动容器

```yaml
version: "3"
services:
  rabbitmq1:
    image: rabbitmq:3-management
    hostname: rabbitmq1
    container_name: rabbitmq1
    networks:
      rabbitmq_net:
        ipv4_address: 172.20.0.2
    ports:
#      - "15672:15672"
      - "5675:5672"
    environment:
      RABBITMQ_DEFAULT_USER: admin
      RABBITMQ_DEFAULT_PASS: Admin@123
      RABBITMQ_ERLANG_COOKIE: rabbitcookie
    restart: always
    volumes:
      - ./rabbitmq1/data:/var/lib/rabbitmq/mnesia

  rabbitmq2:
    image: rabbitmq:3-management
    hostname: rabbitmq2
    container_name: rabbitmq2
    networks:
      rabbitmq_net:
        ipv4_address: 172.20.0.3
    ports:
      - "5673:5672"
    environment:
      RABBITMQ_ERLANG_COOKIE: rabbitcookie
    restart: always
    volumes:
      - ./rabbitmq2/data:/var/lib/rabbitmq/mnesia

  rabbitmq3:
    image: rabbitmq:3-management
    container_name: rabbitmq3
    hostname: rabbitmq3
    networks:
      rabbitmq_net:
        ipv4_address: 172.20.0.4
    ports:
      - "5674:5672"
    environment:
      RABBITMQ_ERLANG_COOKIE: rabbitcookie
    restart: always
    volumes:
      - ./rabbitmq3/data:/var/lib/rabbitmq/mnesia

networks:
  rabbitmq_net:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16

```

```yaml
version: "3"
services:
  haproxy-service:
    image: haproxy:1.7
    container_name: haproxy-service
    network_mode: rabbitmq-cluster_rabbitmq_net
    ports:
      - "5672:5672"
    volumes:
      - ./haproxy_service/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg
    restart: always

  haproxy-web:
    image: haproxy:1.7
    container_name: haproxy-web
    network_mode: rabbitmq-cluster_rabbitmq_net
    ports:
      - "15672:15672"
    volumes:
      - ./haproxy_web/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg
    restart: always

```

```bash
cat haproxy_service/haproxy.cfg
defaults
    log global
    mode tcp
    timeout client 30s                   # 设置客户端超时时间为30秒
    timeout connect 30s                  # 设置连接超时时间为30秒
    timeout server 30s                   # 设置服务器超时时间为30秒

frontend rabbitmq_frontend
    bind *:5672                          # 监听所有IP地址的5672端口
    default_backend rabbitmq_backend     # 默认转发到rabbitmq_backend后端

backend rabbitmq_backend
    mode tcp
    balance roundrobin                   # 使用轮询算法进行负载均衡
    timeout client 30s                   # 设置客户端超时时间为30秒
    timeout server 30s                   # 设置服务器超时时间为30秒
    server rabbitmq1 rabbitmq1:5672 check  # 定义rabbitmq1服务器，使用5672端口，并进行健康检查
    server rabbitmq2 rabbitmq2:5672 check  # 定义rabbitmq2服务器，使用5672端口，并进行健康检查
    server rabbitmq3 rabbitmq3:5672 check  # 定义rabbitmq3服务器，使用5672端口，并进行健康检查
```

```bash
cat haproxy_web/haproxy.cfg
defaults
    log global
    mode tcp
    timeout client 30s                   # 设置客户端超时时间为30秒
    timeout connect 30s                  # 设置连接超时时间为30秒
    timeout server 30s                   # 设置服务器超时时间为30秒

frontend rabbitmq_frontend
    bind *:15672                         # 监听所有IP地址的15672端口
    default_backend rabbitmq_backend     # 默认转发到rabbitmq_backend后端

backend rabbitmq_backend
    mode tcp
    balance roundrobin                   # 使用轮询算法进行负载均衡
    timeout client 30s                   # 设置客户端超时时间为30秒
    timeout server 30s                   # 设置服务器超时时间为30秒
    server rabbitmq1_web rabbitmq1:15672 check  # 定义rabbitmq1管理界面，使用15672端口，并进行健康检查
    server rabbitmq2_web rabbitmq2:15672 check  # 定义rabbitmq2管理界面，使用15672端口，并进行健康检查
    server rabbitmq3_web rabbitmq3:15672 check  # 定义rabbitmq3管理界面，使用15672端口，并进行健康检查
```

3.编写配置文件并执行(嫌麻烦就记标红部分即可)

[root@k8s-master-node1 rabbitmq-cluster]# cat init_rabbitmq.sh

```bash
#!/bin/bash

#build cluster
echo "Starting to build rabbitmq cluster with two ram nodes."
docker exec rabbitmq2 /bin/bash -c 'rabbitmqctl stop_app'
docker exec rabbitmq2 /bin/bash -c 'rabbitmqctl join_cluster --ram rabbit@rabbitmq1'
docker exec rabbitmq2 /bin/bash -c 'rabbitmqctl start_app'

docker exec rabbitmq3 /bin/bash -c 'rabbitmqctl stop_app'
docker exec rabbitmq3 /bin/bash -c 'rabbitmqctl join_cluster --ram rabbit@rabbitmq1'
docker exec rabbitmq3 /bin/bash -c 'rabbitmqctl start_app'

#check cluster status
echo "Check cluster status:"
docker exec rabbitmq1 /bin/bash -c 'rabbitmqctl cluster_status'
```

[root@k8s-master-node1 rabbitmq-cluster]# sh init_rabbitmq.sh

4.到浏览器输入http://master_ip:15672登录出现如下界面

![[1699027858649-60c77441-3d02-4dc5-898d-e0c510ec9e96.png]]

Versions

rabbit@rabbitmq1: RabbitMQ 3.9.11 on Erlang 24.2

rabbit@rabbitmq2: RabbitMQ 3.9.11 on Erlang 24.2

rabbit@rabbitmq3: RabbitMQ 3.9.11 on Erlang 24.2
