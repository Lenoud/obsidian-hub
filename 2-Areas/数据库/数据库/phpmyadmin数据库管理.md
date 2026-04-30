# phpMyAdmin 数据库管理工具

基于 Web 的 MySQL/MariaDB 数据库管理工具，通过 Docker 部署。

## 环境变量说明

| 变量 | 说明 |
| --- | --- |
| `PMA_ARBITRARY` | 设为 `1` 时允许连接任意服务器 |
| `PMA_HOST` | MySQL 服务器地址 |
| `PMA_PORT` | MySQL 服务器端口 |
| `PMA_USER` / `PMA_PASSWORD` | 配置认证模式的用户名和密码 |
| `UPLOAD_LIMIT` | 覆盖上传文件大小限制（默认 2048K） |
| `MEMORY_LIMIT` | 覆盖 PHP 内存限制（默认 512M） |
| `HIDE_PHP_VERSION` | 隐藏 PHP 版本信息 |

## 部署方式一

```yaml
version: '3.9'

services:
  phpmyadmin:
    hostname: phpmyadmin
    container_name: phpmyadmin
    deploy:
      resources:
        limits:
          cpus: "0"
    environment:
      PMA_ARBITRARY: "1"
      PMA_UPLOAD_DIR: /data/upload
      PMA_SAVE_DIR: /data/save
      MEMORY_LIMIT: 3G
      UPLOAD_LIMIT: 10G
    image: phpmyadmin:5.2.1
    network_mode: db_bridge
    ports:
      - "888:80"
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./data/:/data/
```

## 部署方式二（指定 IP）

```yaml
version: '3.0'

services:
  phpmyadmin:
    container_name: phpmyadmin
    deploy:
      resources:
        limits:
          cpus: "0"
    environment:
      PMA_ARBITRARY: "1"
    image: phpmyadmin:5.2.1
    networks:
      db_bridge:
        ipv4_address: 172.20.0.3
    ports:
      - "888:80"
    restart: always
    volumes:
      - ./conf.d/uploads.ini:/usr/local/etc/php/conf.d/uploads.ini
      - ./conf.inc/config.user.inc.php:/etc/phpmyadmin/config.user.inc.php
```
