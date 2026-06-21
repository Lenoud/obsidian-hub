# DBGate 数据库管理工具

跨平台的开源数据库 Web 管理界面，支持 MySQL、PostgreSQL、SQL Server、MongoDB、SQLite 等多种数据库连接。

## 部署

```yaml
version: '3'
services:
  dbgate:
    hostname: dbgate
    container_name: dbgate
    image: dbgate/dbgate:5.3.1
    restart: always
    ports:
      - 890:3000
    volumes:
      - ./dbgate-data:/root/.dbgate
      - /etc/localtime:/etc/localtime:ro
    network_mode: db_bridge
```

## 说明

| 参数 | 值 | 说明 |
| --- | --- | --- |
| `image` | `dbgate/dbgate:5.3.1` | DBGate 5.3.1 版本镜像 |
| `ports` | `890:3000` | 宿主机 890 映射容器 3000，访问地址 `http://<IP>:890` |
| `volumes` | `./dbgate-data` | 配置与连接历史持久化目录 |
| `network_mode` | `db_bridge` | 与数据库容器同网络，可直接用容器名连接 |

## 相关笔记
- [[MOC-Docker]]
- [[MOC-数据库]]
- [[adminer数据库管理工具]] — 另一款轻量级数据库管理工具
- [[phpmyadmin数据库管理]] — 专注 MySQL/MariaDB 的 Web 管理工具
