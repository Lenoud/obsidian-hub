# Redis 部署

开源的高性能键值对内存数据库，广泛用于缓存、消息队列和实时数据处理场景。

## 部署

```yaml
services:
  redis:
    image: redis:7.4.1
    restart: always
    container_name: redis74
    network_mode: db_bridge
    ports:
      - 6379:6379
    command: |
      sh -c ' if [ -z "000000" ]; then
        redis-server /etc/redis/redis.conf
      else
        redis-server /etc/redis/redis.conf --requirepass 000000
      fi'
    volumes:
      - ./data:/data
      - ./conf/redis.conf:/etc/redis/redis.conf
      - ./logs:/logs
      - /etc/localtime:/etc/localtime:ro
    labels:
      createdBy: Apps
networks: {}
```

## 说明

- 访问端口：`6379`
- 密码认证：`000000`（通过 `--requirepass` 参数设置）
- 自定义配置：`./conf/redis.conf` 映射到 `/etc/redis/redis.conf`
- 数据持久化：`./data` 映射到 `/data`
- 日志目录：`./logs` 映射到 `/logs`
- `network_mode: db_bridge`：使用自定义网络

## 相关笔记
- [[redis]]
- [[MOC-Docker]]
