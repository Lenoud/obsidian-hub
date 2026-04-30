# Redis (Bitnami) 主从部署

基于 Bitnami 镜像的 Redis 7.4 主从复制架构，包含一个主节点和一个从节点。

## 部署

### compose.yaml

```yaml
services:
  redis-master:
    hostname: redis01
    container_name: redis01
    restart: unless-stopped
    image: bitnami/redis:7.4.1
    ports:
      - 6379:6379
    environment:
      - REDIS_REPLICATION_MODE=master
      - REDIS_PASSWORD=000000
    volumes:
      - ./master_data/:/bitnami/redis/data/
    networks:
      - db_bridge
  redis-slave:
    hostname: redis02
    container_name: redis02
    restart: unless-stopped
    image: bitnami/redis:7.4.1
    ports:
      - 6380:6379
    environment:
      - REDIS_REPLICATION_MODE=slave
      - REDIS_MASTER_HOST=redis01
      - REDIS_MASTER_PORT_NUMBER=6379
      - REDIS_MASTER_PASSWORD=000000
      - REDIS_PASSWORD=000000
    volumes:
      - ./slave_data/:/bitnami/redis/data/
    networks:
      - db_bridge
    depends_on:
      - redis-master
networks:
  db_bridge:
    external: true
```

### 启动前准备

```bash
#在compose当前目录创建两个目录
mkdir master_data slave_data
#赋予redis容器user权限，用户名1001
chown 1001:1001 master_data
chown 1001:1001 slave_data

#启动容器即可
docker-compose  up -d
```

## 说明

- 主节点端口：`6379`（redis01）
- 从节点端口：`6380`（redis02）
- `REDIS_REPLICATION_MODE`：节点角色（`master` 或 `slave`）
- `REDIS_PASSWORD`：Redis 密码
- `REDIS_MASTER_HOST`：从节点连接的主节点主机名
- `REDIS_MASTER_PASSWORD`：从节点连接主节点的密码
- 数据持久化：`./master_data` 和 `./slave_data`
- 启动前需执行 `chown 1001:1001` 赋予目录权限

### 环境变量
#### 可自定义的环境变量
| 名称 | 描述 | 默认值 |
| --- | --- | --- |
| REDIS_DATA_DIR | Redis 数据目录 | ${REDIS_VOLUME_DIR}/data |
| REDIS_OVERRIDES_FILE | Redis 配置覆盖文件 | ${REDIS_MOUNTED_CONF_DIR}/overrides.conf |
| REDIS_DISABLE_COMMANDS | 禁用的 Redis 命令 | nil |
| REDIS_DATABASE | 默认 Redis 数据库 | redis |
| REDIS_AOF_ENABLED | 启用 AOF | yes |
| REDIS_RDB_POLICY | 启用 RDB 策略持久化 | nil |
| REDIS_RDB_POLICY_DISABLED | 是否禁用 RDB 策略持久化 | no |
| REDIS_MASTER_HOST | Redis 主节点主机（从节点使用） | nil |
| REDIS_MASTER_PORT_NUMBER | Redis 主节点端口（从节点使用） | 6379 |
| REDIS_PORT_NUMBER | Redis 端口号 | $REDIS_DEFAULT_PORT_NUMBER |
| REDIS_ALLOW_REMOTE_CONNECTIONS | 是否允许远程连接服务 | yes |
| REDIS_REPLICATION_MODE | Redis 复制模式（值：master, slave） | nil |
| REDIS_REPLICA_IP | 复制节点的 IP 地址 | nil |
| REDIS_REPLICA_PORT | 复制节点的端口号 | nil |
| REDIS_EXTRA_FLAGS | 传递给 `redis-server` 命令的额外标志 | nil |
| ALLOW_EMPTY_PASSWORD | 是否允许无密码访问 | no |
| REDIS_PASSWORD | Redis 密码 | nil |
| REDIS_MASTER_PASSWORD | Redis 主节点密码 | nil |
| REDIS_ACLFILE | Redis ACL 文件 | nil |
| REDIS_IO_THREADS_DO_READS | 启用多线程读取套接字 | nil |
| REDIS_IO_THREADS | 线程数量 | nil |
| REDIS_TLS_ENABLED | 启用 TLS | no |
| REDIS_TLS_PORT_NUMBER | Redis TLS 端口（需启用 TLS） | 6379 |
| REDIS_TLS_CERT_FILE | Redis TLS 证书文件 | nil |
| REDIS_TLS_CA_DIR | 包含 TLS CA 证书的目录 | nil |
| REDIS_TLS_KEY_FILE | Redis TLS 密钥文件 | nil |
| REDIS_TLS_KEY_FILE_PASS | Redis TLS 密钥文件密码 | nil |
| REDIS_TLS_CA_FILE | Redis TLS CA 文件 | nil |
| REDIS_TLS_DH_PARAMS_FILE | Redis TLS DH 参数文件 | nil |
| REDIS_TLS_AUTH_CLIENTS | 启用 Redis TLS 客户端身份验证 | yes |
| REDIS_SENTINEL_MASTER_NAME | Redis Sentinel 主节点名称 | nil |
| REDIS_SENTINEL_HOST | Redis Sentinel 主机 | nil |
| REDIS_SENTINEL_PORT_NUMBER | Redis Sentinel 主机端口（从节点使用） | 26379 |

#### 只读环境变量
| 名称 | 描述 | 值 |
| --- | --- | --- |
| REDIS_VOLUME_DIR | 持久化基础目录 | /bitnami/redis |
| REDIS_BASE_DIR | Redis 安装目录 | ${BITNAMI_ROOT_DIR}/redis |
| REDIS_CONF_DIR | Redis 配置目录 | ${REDIS_BASE_DIR}/etc |
| REDIS_DEFAULT_CONF_DIR | Redis 默认配置目录 | ${REDIS_BASE_DIR}/etc.default |
| REDIS_MOUNTED_CONF_DIR | Redis 挂载的配置目录 | ${REDIS_BASE_DIR}/mounted-etc |
| REDIS_CONF_FILE | Redis 配置文件 | ${REDIS_CONF_DIR}/redis.conf |
| REDIS_LOG_DIR | Redis 日志目录 | ${REDIS_BASE_DIR}/logs |
| REDIS_LOG_FILE | Redis 日志文件 | ${REDIS_LOG_DIR}/redis.log |
| REDIS_TMP_DIR | Redis 临时目录 | ${REDIS_BASE_DIR}/tmp |
| REDIS_PID_FILE | Redis PID 文件 | ${REDIS_TMP_DIR}/redis.pid |
| REDIS_BIN_DIR | Redis 可执行文件目录 | ${REDIS_BASE_DIR}/bin |
| REDIS_DAEMON_USER | Redis 系统用户 | redis |
| REDIS_DAEMON_GROUP | Redis 系统用户组 | redis |
| REDIS_DEFAULT_PORT_NUMBER | Redis 端口号（构建时） | 6379 |
