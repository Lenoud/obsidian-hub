# Adminer 数据库管理工具

轻量级单文件数据库管理 Web 界面，支持 MySQL、MariaDB、PostgreSQL、SQLite、SQL Server 等多种数据库，部署简单、资源占用低。

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

| 参数 | 值 | 说明 |
| --- | --- | --- |
| `image` | `adminer:4.8.1` | Adminer 4.8.1 镜像 |
| `ports` | `889:8080` | 宿主机 889 映射容器 8080，访问地址 `http://<IP>:889` |
| `ADMINER_DEFAULT_SERVER` | 数据库 IP/容器名 | 登录页默认填充的服务器地址，改成实际数据库 |
| `volumes` | `./plugins-enabled` | 启用的插件目录（Adminer 通过插件扩展功能） |
| `network_mode` | `db_bridge` | 与数据库容器同网络，可直接用容器名连接 |

## 扩展资源

- **插件下载**：https://www.adminer.org/en/plugins/
- **暗色主题**：下载 [adminer.css](https://raw.githubusercontent.com/Niyko/Hydra-Dark-Theme-for-Adminer/master/adminer.css) 放入 `/var/www/html/` 目录。

## 相关笔记
- [[MOC-Docker]]
- [[MOC-数据库]]
- [[dbgate数据库管理工具]] — 另一款多数据库管理工具
- [[phpmyadmin数据库管理]] — 专注 MySQL/MariaDB 的 Web 管理工具
