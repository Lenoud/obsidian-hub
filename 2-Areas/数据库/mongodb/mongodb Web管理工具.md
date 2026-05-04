# MongoDB Express Web 管理工具

使用 mongo-express 提供 MongoDB 的 Web 管理界面。

## 部署

```yaml
services:
  mongodb-express:
    container_name: mongodb-express
    restart: unless-stopped
    image: mongo-express:1.0.2-20
    network_mode: db_bridge
    ports:
      - 892:8081
    environment:
      ME_CONFIG_BASICAUTH: true
      ME_CONFIG_BASICAUTH_USERNAME: root
      ME_CONFIG_BASICAUTH_PASSWORD: password123
      ME_CONFIG_MONGODB_URL: mongodb://root:password123@192.168.50.52:27017
```
