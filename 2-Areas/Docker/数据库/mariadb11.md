# MariaDB 11 数据库

MySQL 的一个流行分支，由 MySQL 原始开发者维护，完全兼容 MySQL 协议和生态。

## 部署

```yaml
version: "3.9"
services:
  mariadb11:
    hostname: mariadb11
    container_name: mariadb11
    image: mariadb:11.4.2
    restart: always
    environment:
      MARIADB_ROOT_PASSWORD: "000000"
    ports:
      - 3308:3306
    volumes:
      - ./mariadb11_data/:/var/lib/mysql/
      - /etc/localtime:/etc/localtime:ro
    network_mode: db_bridge
```

## 说明

- 访问端口：`3308`（映射到容器内 `3306`）
- `MARIADB_ROOT_PASSWORD`：root 用户密码
- 数据持久化：`./mariadb11_data` 映射到 `/var/lib/mysql/`
- `network_mode: db_bridge`：使用自定义网络
