# Docker 部署 Redis

使用 Docker Compose 部署 Redis 7.4，支持密码认证和自定义配置文件。

## 部署配置

```yaml
services:
  redis:
    image: redis:7.4.1
    restart: always
    container_name: redis74
    hostname: redis
    network_mode: db_bridge
    ports:
      - 6379:6379
    command: >
      sh -c '
      if [ -z "000000" ]; then
        redis-server /etc/redis/redis.conf
      else
        redis-server /etc/redis/redis.conf --requirepass 000000
      fi'
    volumes:
      - ./data:/data
      - ./conf/redis.conf:/etc/redis/redis.conf
      - ./logs:/logs
    labels:
      createdBy: "Apps"
```

## 说明

| 参数 | 值 | 说明 |
| --- | --- | --- |
| `image` | `redis:7.4.1` | Redis 7.4 版本 |
| `--requirepass` | 自定义密码 | Redis 认证密码 |
| `volumes` | `./conf/redis.conf` | 自定义 Redis 配置文件 |
| `ports` | `6379:6379` | Redis 默认端口 |

## 相关笔记
- [[MOC-Docker]]
- [[redis]]
