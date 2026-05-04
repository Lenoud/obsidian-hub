# MongoDB (Bitnami) 数据库

基于 Bitnami 镜像的 MongoDB 7.0 文档数据库，附带 mongo-express 管理 Web 界面。

## 部署

```yaml
version: "3.9"
services:
  mongodb:
    image: docker.io/bitnami/mongodb:7.0.14
    restart: always
    container_name: mongodb7
    user: root
    ports:
      - 27017:27017
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb
      - MONGODB_ROOT_PASSWORD=000000
    volumes:
      - ./mongodb7_data:/bitnami/mongodb
      - /etc/localtime:/etc/localtime:ro
    network_mode: db_bridge
  mongdbmanager:
    container_name: mongomg
    restart: always
    image: mongo-express:1.0.2-20
    network_mode: db_bridge
    ports:
      - 892:8081
    environment:
      ME_CONFIG_BASICAUTH: true
      ME_CONFIG_BASICAUTH_USERNAME: root
      ME_CONFIG_BASICAUTH_PASSWORD: 000000
      ME_CONFIG_MONGODB_URL: mongodb://root:000000@mongodb7:27017
```

## 说明

- MongoDB 访问端口：`27017`
- Mongo Express 管理界面：`http://<IP>:892`
- `MONGODB_ROOT_PASSWORD`：MongoDB root 用户密码
- `MONGODB_ADVERTISED_HOSTNAME`：MongoDB 广播主机名
- `ME_CONFIG_BASICAUTH_USERNAME` / `ME_CONFIG_BASICAUTH_PASSWORD`：管理界面登录凭据
- `ME_CONFIG_MONGODB_URL`：管理界面连接 MongoDB 的地址
- 数据持久化：`./mongodb7_data` 映射到 `/bitnami/mongodb`

### 环境变量
**可自定义的环境变量**

| 名称 | 描述 | 默认值 |
| --- | --- | --- |
| **MONGODB_MOUNTED_CONF_DIR** | 用于包含自定义配置文件的目录（覆盖默认生成的配置文件） | `${MONGODB_VOLUME_DIR}/conf` |
| **MONGODB_INIT_RETRY_ATTEMPTS** | 检查服务初始化状态时的最大重试次数 | 7 |
| **MONGODB_INIT_RETRY_DELAY** | 每次重试检查服务初始化状态时的等待时间（秒） | 5 |
| **MONGODB_PORT_NUMBER** | MongoDB 的端口号 | `$MONGODB_DEFAULT_PORT_NUMBER` |
| **MONGODB_ENABLE_MAJORITY_READ** | 启用 MongoDB 操作中的多数读取 | true |
| **MONGODB_DEFAULT_ENABLE_MAJORITY_READ** | 默认启用 MongoDB 操作中的多数读取（在构建时设置） | true |
| **MONGODB_EXTRA_FLAGS** | MongoDB 初始化时的额外标志 | nil（无） |
| **MONGODB_ENABLE_NUMACTL** | 使用 numactl 执行命令 | false |
| **MONGODB_SHELL_EXTRA_FLAGS** | 使用 MongoDB 客户端进行初始化时的额外标志（在挂载初始化脚本时有用） | nil（无） |
| **MONGODB_ADVERTISED_HOSTNAME** | 用于广告 MongoDB 服务的主机名 | nil（无） |
| **MONGODB_ADVERTISE_IP** | 是否将广告主机名设置为容器的 IP | false |
| **MONGODB_ADVERTISED_PORT_NUMBER** | 广告的 MongoDB 端口号（如果你有代理转发请求到容器，建议设置此环境变量） | nil（无） |
| **MONGODB_DISABLE_JAVASCRIPT** | 禁用 MongoDB 服务器端的 JavaScript 执行 | no |
| **MONGODB_ENABLE_JOURNAL** | 启用 MongoDB 的日志功能 | nil（无） |
| **MONGODB_DISABLE_SYSTEM_LOG** | 禁用 MongoDB 守护进程的系统日志 | nil（无） |
| **MONGODB_ENABLE_DIRECTORY_PER_DB** | 为每个数据库使用单独的文件夹存储数据 | nil（无） |
| **MONGODB_ENABLE_IPV6** | 使用 IPv6 进行数据库连接 | nil（无） |
| **MONGODB_SYSTEM_LOG_VERBOSITY** | MongoDB 守护进程日志级别 | nil（无） |
| **MONGODB_ROOT_USER** | MongoDB 的 root 用户名 | root |
| **MONGODB_ROOT_PASSWORD** | MongoDB 的 root 用户密码 | nil（无） |
| **MONGODB_USERNAME** | 在初始化时生成的用户 | nil（无） |
| **MONGODB_PASSWORD** | 为 MONGODB_USERNAME 指定的非 root 用户设置的密码 | nil（无） |
| **MONGODB_DATABASE** | 在初始化时创建的数据库名称 | nil（无） |
| **MONGODB_METRICS_USERNAME** | 用于指标收集的用户，例如与 mongodb_exporter 配合使用 | nil（无） |
| **MONGODB_METRICS_PASSWORD** | 为 MONGODB_METRICS_USERNAME 指定的非 root 用户设置的密码 | nil（无） |
| **MONGODB_EXTRA_USERNAMES** | 用逗号或分号分隔的额外用户列表，初始化时创建 | nil（无） |
| **MONGODB_EXTRA_PASSWORDS** | 用逗号或分号分隔的密码列表，针对 MONGODB_EXTRA_USERNAMES 中指定的用户 | nil（无） |
| **MONGODB_EXTRA_DATABASES** | 用逗号或分号分隔的数据库列表，初始化时为 MONGODB_EXTRA_USERNAMES 指定的用户创建 | nil（无） |
| **ALLOW_EMPTY_PASSWORD** | 是否允许在没有设置密码的情况下访问 MongoDB | no |
| **MONGODB_REPLICA_SET_MODE** | MongoDB 副本集模式，可以是 primary、secondary 或 arbiter | nil（无） |
| **MONGODB_REPLICA_SET_NAME** | MongoDB 副本集的名称 | `$MONGODB_DEFAULT_REPLICA_SET_NAME` |
| **MONGODB_REPLICA_SET_KEY** | MongoDB 副本集密钥 | nil（无） |
| **MONGODB_INITIAL_PRIMARY_HOST** | 副本集主节点的主机名（对于 arbiter 和 secondary 节点来说必要） | nil（无） |
| **MONGODB_INITIAL_PRIMARY_PORT_NUMBER** | 副本集主节点的端口号（对于 arbiter 和 secondary 节点来说必要） | 27017 |
| **MONGODB_INITIAL_PRIMARY_ROOT_PASSWORD** | 副本集主节点 root 用户密码（对于 arbiter 和 secondary 节点来说必要） | nil（无） |
| **MONGODB_INITIAL_PRIMARY_ROOT_USER** | 副本集主节点 root 用户名（对于 arbiter 和 secondary 节点来说必要） | root |
| **MONGODB_SET_SECONDARY_OK** | 将节点标记为可读。适用于 PVC 丢失的情况 | no |
| **MONGODB_DISABLE_ENFORCE_AUTH** | 默认启用 MongoDB 身份验证。如果设置为 true，MongoDB 将不强制身份验证 | false |

### 只读环境变量
| 名称 | 描述 | 默认值 |
| --- | --- | --- |
| **MONGODB_VOLUME_DIR** | 持久化存储的基础目录 | `$BITNAMI_VOLUME_DIR/mongodb` |
| **MONGODB_BASE_DIR** | MongoDB 安装目录 | `$BITNAMI_ROOT_DIR/mongodb` |
| **MONGODB_CONF_DIR** | MongoDB 配置文件目录 | `$MONGODB_BASE_DIR/conf` |
| **MONGODB_DEFAULT_CONF_DIR** | MongoDB 默认配置文件目录 | `$MONGODB_BASE_DIR/conf.default` |
| **MONGODB_LOG_DIR** | MongoDB 日志目录 | `$MONGODB_BASE_DIR/logs` |
| **MONGODB_DATA_DIR** | MongoDB 数据目录 | `${MONGODB_VOLUME_DIR}/data` |
| **MONGODB_TMP_DIR** | MongoDB 临时目录 | `$MONGODB_BASE_DIR/tmp` |
| **MONGODB_BIN_DIR** | MongoDB 可执行文件目录 | `$MONGODB_BASE_DIR/bin` |
| **MONGODB_TEMPLATES_DIR** | 存放 `mongodb.conf` 模板文件的目录 | `$MONGODB_BASE_DIR/templates` |
| **MONGODB_MONGOD_TEMPLATES_FILE** | `mongodb.conf` 模板文件的路径 | `$MONGODB_TEMPLATES_DIR/mongodb.conf.tpl` |
| **MONGODB_CONF_FILE** | MongoDB 配置文件的路径 | `$MONGODB_CONF_DIR/mongodb.conf` |
| **MONGODB_KEY_FILE** | MongoDB 副本集密钥文件的路径 | `$MONGODB_CONF_DIR/keyfile` |
| **MONGODB_DB_SHELL_FILE** | MongoDB `dbshell` 文件路径 | `/.dbshell` |
| **MONGODB_RC_FILE** | MongoDB `rc` 配置文件路径 | `/.mongorc.js` |
| **MONGOSH_DIR** | `mongosh`（MongoDB shell）目录的路径 | `/.mongodb` |
| **MONGOSH_RC_FILE** | `mongosh` 配置文件路径 | `/.mongoshrc.js` |
| **MONGODB_PID_FILE** | MongoDB PID 文件路径 | `$MONGODB_TMP_DIR/mongodb.pid` |
| **MONGODB_LOG_FILE** | MongoDB 日志文件路径 | `$MONGODB_LOG_DIR/mongodb.log` |
| **MONGODB_INITSCRIPTS_DIR** | MongoDB 容器初始化脚本目录 | `/docker-entrypoint-initdb.d` |
| **MONGODB_DAEMON_USER** | MongoDB 系统用户 | `mongo` |
| **MONGODB_DAEMON_GROUP** | MongoDB 系统用户组 | `mongo` |
| **MONGODB_DEFAULT_PORT_NUMBER** | 构建时指定的 MongoDB 端口号 | `27017` |
| **MONGODB_DEFAULT_ENABLE_JOURNAL** | 构建时是否启用 MongoDB 日志功能 | `true` |
| **MONGODB_DEFAULT_DISABLE_SYSTEM_LOG** | 构建时是否禁用 MongoDB 守护进程的系统日志 | `false` |
| **MONGODB_DEFAULT_ENABLE_DIRECTORY_PER_DB** | 构建时是否为每个数据库使用单独的目录存储数据 | `false` |
| **MONGODB_DEFAULT_ENABLE_IPV6** | 构建时是否使用 IPv6 进行数据库连接 | `false` |
| **MONGODB_DEFAULT_SYSTEM_LOG_VERBOSITY** | 构建时设置的 MongoDB 守护进程日志级别 | `0` |
| **MONGODB_DEFAULT_REPLICA_SET_NAME** | 构建时指定的 MongoDB 副本集名称 | `replicaset` |

## 相关笔记
- [[docker部署mongodb主从哨兵]]
- [[mongodb]]
- [[MOC-Docker]]
