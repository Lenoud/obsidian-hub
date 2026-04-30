# DBGate 数据库管理工具

开源的数据库 Web 管理界面，支持多种数据库连接。

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
      - 8880:3000
    volumes:
      - ./dbgate-data:/root/.dbgate
      - /etc/localtime:/etc/localtime:ro
    network_mode: db_bridge
```
