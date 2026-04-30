# Docker 部署 MongoDB 单节点

使用 Bitnami MongoDB 镜像通过 Docker Compose 部署单节点 MongoDB 服务，附带 mongo-express 管理界面。

## MongoDB 7.0 版本（含管理界面）

```yaml
version: '3.9'

services:
  mongodb:
    image: docker.io/bitnami/mongodb:7.0.14
    restart: always
    container_name: mongodb7
    privileged: true
    ports:
      - 27017:27017
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb
      - MONGODB_ROOT_PASSWORD=password123
    volumes:
      - ./mongodb7_data:/bitnami/mongodb
      - /etc/localtime:/etc/localtime:ro
    network_mode: db_bridge

  mongo-express:
    container_name: mongomg
    restart: always
    image: mongo-express:1.0.2-20
    network_mode: db_bridge
    ports:
      - 892:8081
    environment:
      ME_CONFIG_BASICAUTH: true
      ME_CONFIG_BASICAUTH_USERNAME: root
      ME_CONFIG_BASICAUTH_PASSWORD: password123
      ME_CONFIG_MONGODB_URL: mongodb://root:password123@mongodb7:27017
```

如遇权限报错：

```bash
chmod 777 -R mongodb_data/
```

## MongoDB 4.4.15 版本

```yaml
version: '3.9'

services:
  mongodb:
    image: 'docker.io/bitnami/mongodb:4.4.15'
    restart: always
    container_name: mongodb
    privileged: true
    ports:
      - 27018:27017
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb
      - MONGODB_ROOT_PASSWORD=password123
    volumes:
      - './mongodb_data:/bitnami/mongodb'
      - /etc/localtime:/etc/localtime:ro
    network_mode: db_bridge
```

## 说明

| 参数 | 说明 |
| --- | --- |
| `MONGODB_ROOT_PASSWORD` | root 用户密码 |
| `MONGODB_ADVERTISED_HOSTNAME` | MongoDB 对外广播的主机名 |
| `mongo-express` 端口 | `892:8081`，通过浏览器访问管理界面 |
