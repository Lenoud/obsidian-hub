# Docker 部署 MariaDB

使用 Docker Compose 部署 MariaDB 11.4 数据库服务。

## 部署配置

```yaml
version: '3.9'

services:
  mariadb11:
    hostname: mariadb11
    container_name: mariadb11
    image: mariadb:11.4.2
    restart: always
    environment:
      MARIADB_ROOT_PASSWORD: '000000'
    ports:
      - 3308:3306
    volumes:
      - ./mariadb11_data/:/var/lib/mysql/
      - /etc/localtime:/etc/localtime:ro
    network_mode: db_bridge
```

## 说明

| 参数 | 值 | 说明 |
| --- | --- | --- |
| `image` | `mariadb:11.4.2` | MariaDB 11.4 版本镜像 |
| `MARIADB_ROOT_PASSWORD` | 自定义 | root 用户密码 |
| `ports` | `3308:3306` | 宿主机 3308 映射容器 3306 |
| `volumes` | `./mariadb11_data/` | 数据持久化目录 |

## 相关笔记
- [[Mariadb]]
- [[mariadb数据库主从配置]]
- [[MOC-Docker]]
- [[MOC-数据库]]
