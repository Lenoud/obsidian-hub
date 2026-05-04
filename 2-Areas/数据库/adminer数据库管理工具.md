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
      - 8888:8080
    network_mode: db_bridge
    environment:
      - ADMINER_DEFAULT_SERVER=192.168.100.120
    volumes:
      - ./plugins-enabled/:/var/www/html/plugins-enabled/
      - /etc/localtime:/etc/localtime:ro
```

插件下载：https://www.adminer.org/en/plugins/

暗色主题：下载 [adminer.css](https://raw.githubusercontent.com/Niyko/Hydra-Dark-Theme-for-Adminer/master/adminer.css) 放入 `/var/www/html/` 目录。
