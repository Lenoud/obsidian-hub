# Docker 部署 MySQL

使用 Docker Compose 部署 MySQL 5.7 和 MySQL 8.0 两个版本的数据库服务。

## MySQL 5.7

```yaml
version: '3.8'

services:
  mysql57:
    hostname: mysql57
    container_name: mysql57
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: "000000"
    ports:
      - "3306:3306"
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./mysql5-data:/var/lib/mysql
      - ./root:/root
    networks:
      db_bridge:
        ipv4_address: 172.20.0.254

networks:
  db_bridge:
    name: db_bridge
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16
```

## MySQL 8.0

```yaml
version: '3.8'

services:
  mysql8:
    hostname: mysql8
    container_name: mysql8
    image: mysql:8.0.36
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: "000000"
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./mysql8-data:/var/lib/mysql
    network_mode: db_bridge
    ports:
      - "3307:3306"
```

## 说明

| 参数 | MySQL 5.7 | MySQL 8.0 |
| --- | --- | --- |
| `image` | `mysql:5.7` | `mysql:8.0.36` |
| `ports` | `3306:3306` | `3307:3306` |
| `network_mode` | 自定义网桥（指定 IP） | `db_bridge` |
| 数据目录 | `./mysql5-data` | `./mysql8-data` |

## 相关笔记
- [[mysql57]]
- [[mysql8]]
- [[MOC-数据库]]
- [[MOC-Docker]]
- [[Docker网络详解]]
