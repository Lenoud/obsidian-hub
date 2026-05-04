# DbGate 数据库管理工具

跨平台的数据库管理 Web 界面，支持 MySQL、PostgreSQL、SQL Server、MongoDB、SQLite 等多种数据库。

## 部署

```yaml
version: "3"
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

- 访问地址：`http://<IP>:890`
- 端口映射：`890` -> `3000`
- 数据持久化：`./dbgate-data` 映射到 `/root/.dbgate`
- `network_mode: db_bridge`：使用自定义网络，确保与数据库容器在同一网络
