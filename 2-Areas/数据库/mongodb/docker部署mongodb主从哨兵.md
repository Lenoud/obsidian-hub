# Docker 部署 MongoDB 主从哨兵（Replica Set）

使用 Bitnami MongoDB 镜像通过 Docker Compose 部署一主一从一仲裁的复制集架构。

## MongoDB 4.4.15 版本

```yaml
version: '3.9'

services:
  mongodb-primary:
    image: 'docker.io/bitnami/mongodb:4.4.15'
    restart: always
    container_name: mongodb-primary
    ports:
      - 27017:27017
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-primary
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
    privileged: true
    volumes:
      - './mongodb_data:/bitnami/mongodb'
      - /etc/localtime:/etc/localtime:ro
    networks:
      mogo_db_bridge:
        ipv4_address: 172.19.0.2

  mongodb-secondary:
    image: 'docker.io/bitnami/mongodb:4.4.15'
    restart: always
    container_name: mongodb-secondary
    depends_on:
      - mongodb-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-secondary
      - MONGODB_REPLICA_SET_MODE=secondary
      - MONGODB_INITIAL_PRIMARY_HOST=mongodb-primary
      - MONGODB_INITIAL_PRIMARY_PORT_NUMBER=27017
      - MONGODB_INITIAL_PRIMARY_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
    volumes:
      - /etc/localtime:/etc/localtime:ro
    networks:
      mogo_db_bridge:
        ipv4_address: 172.19.0.3

  mongodb-arbiter:
    image: 'docker.io/bitnami/mongodb:4.4.15'
    restart: always
    container_name: mongodb-arbiter
    depends_on:
      - mongodb-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-arbiter
      - MONGODB_REPLICA_SET_MODE=arbiter
      - MONGODB_INITIAL_PRIMARY_HOST=mongodb-primary
      - MONGODB_INITIAL_PRIMARY_PORT_NUMBER=27017
      - MONGODB_INITIAL_PRIMARY_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
    volumes:
      - /etc/localtime:/etc/localtime:ro
    networks:
      mogo_db_bridge:
        ipv4_address: 172.19.0.4

networks:
  mogo_db_bridge:
    name: mogo_db_bridge
    driver: bridge
    ipam:
      config:
        - subnet: 172.19.0.0/16
```

## MongoDB 7.0.12 版本

```yaml
version: '3.9'

services:
  mongodb-primary:
    image: 'docker.io/bitnami/mongodb:7.0.12'
    restart: always
    container_name: mongodb-primary
    privileged: true
    ports:
      - 27017:27017
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-primary
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
    volumes:
      - './mongodb_data:/bitnami/mongodb'
      - /etc/localtime:/etc/localtime:ro
    network_mode: db_bridge

  mongodb-secondary:
    image: 'docker.io/bitnami/mongodb:7.0.12'
    restart: always
    container_name: mongodb-secondary
    depends_on:
      - mongodb-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-secondary
      - MONGODB_REPLICA_SET_MODE=secondary
      - MONGODB_INITIAL_PRIMARY_HOST=mongodb-primary
      - MONGODB_INITIAL_PRIMARY_PORT_NUMBER=27017
      - MONGODB_INITIAL_PRIMARY_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
    volumes:
      - /etc/localtime:/etc/localtime:ro
    network_mode: db_bridge

  mongodb-arbiter:
    image: 'docker.io/bitnami/mongodb:7.0.12'
    restart: always
    container_name: mongodb-arbiter
    depends_on:
      - mongodb-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-arbiter
      - MONGODB_REPLICA_SET_MODE=arbiter
      - MONGODB_INITIAL_PRIMARY_HOST=mongodb-primary
      - MONGODB_INITIAL_PRIMARY_PORT_NUMBER=27017
      - MONGODB_INITIAL_PRIMARY_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
    volumes:
      - /etc/localtime:/etc/localtime:ro
    network_mode: db_bridge
```

## 连接方式

```bash
docker exec -it mongodb-primary bash
mongo admin -u root -p password123 --authenticationDatabase admin
```

连接 URL：`mongodb://root:password123@localhost:27017/admin`

## 说明

| 参数 | 说明 |
| --- | --- |
| `MONGODB_REPLICA_SET_MODE` | 节点角色：`primary`、`secondary`、`arbiter` |
| `MONGODB_ROOT_PASSWORD` | root 用户密码 |
| `MONGODB_REPLICA_SET_KEY` | 复制集认证密钥，所有节点必须一致 |
| `MONGODB_INITIAL_PRIMARY_HOST` | 从节点/仲裁节点指定主节点地址 |
