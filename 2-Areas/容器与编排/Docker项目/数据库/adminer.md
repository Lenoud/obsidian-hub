# Adminer 数据库管理工具

轻量级数据库管理 Web 界面，支持 MySQL、MariaDB、PostgreSQL、SQLite 等多种数据库。

## 部署

```yaml
version: "3.9"
services:
  adminer:
    hostname: adminer
    container_name: adminer
    image: adminer:4.8.1
    restart: always
    ports:
      - 889:8080
    network_mode: db_bridge
    environment:
      - ADMINER_DEFAULT_SERVER=192.168.50.52
    volumes:
      - ./plugins-enabled/:/var/www/html/plugins-enabled/
      - /etc/localtime:/etc/localtime:ro
```

## 说明

- 访问地址：`http://<IP>:889`
- `ADMINER_DEFAULT_SERVER`：默认连接的数据库服务器地址
- `network_mode: db_bridge`：使用自定义网络，确保与数据库容器在同一网络
