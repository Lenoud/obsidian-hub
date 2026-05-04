# Logstash (Bitnami) 日志处理

基于 Bitnami 镜像的 Logstash 日志收集和处理管道，支持从多种来源接收数据并转换后发送到指定输出。

## 部署

### compose.yaml

```yaml

```

### .env 配置

```ini

```

## 环境变量

### 可配置环境变量

| 名称 | 描述 | 默认值 |
| --- | --- | --- |
| LOGSTASH_PIPELINE_CONF_FILENAME | Logstash 管道文件的名称 | logstash.conf |
| LOGSTASH_BIND_ADDRESS | Logstash 监听地址 | 0.0.0.0 |
| LOGSTASH_EXPOSE_API | 是否暴露 Logstash API | no |
| LOGSTASH_API_PORT_NUMBER | Logstash API 端口号 | 9600 |
| LOGSTASH_PIPELINE_CONF_STRING | 以字符串形式提供的 Logstash 管道配置 | nil |
| LOGSTASH_PLUGINS | 需要安装的 Logstash 插件列表 | nil |
| LOGSTASH_EXTRA_FLAGS | 运行 Logstash 服务器的额外参数 | nil |
| LOGSTASH_HEAP_SIZE | Logstash 堆大小 | 1024m |
| LOGSTASH_MAX_ALLOWED_MEMORY_PERCENTAGE | Logstash 允许的最大内存百分比 | 100 |
| LOGSTASH_MAX_ALLOWED_MEMORY | Logstash 允许的最大内存量（以兆字节计） | nil |
| LOGSTASH_ENABLE_MULTIPLE_PIPELINES | 是否启用多个管道支持 | no |
| LOGSTASH_ENABLE_BEATS_INPUT | 是否监听入站 Beats 连接 | no |
| LOGSTASH_BEATS_PORT_NUMBER | 监听入站 Beats 连接的端口号 | 5044 |
| LOGSTASH_ENABLE_GELF_INPUT | 是否监听入站 Gelf 连接 | no |
| LOGSTASH_GELF_PORT_NUMBER | 监听入站 Gelf 连接的端口号 | 12201 |
| LOGSTASH_ENABLE_HTTP_INPUT | 是否监听入站 HTTP 连接 | yes |
| LOGSTASH_HTTP_PORT_NUMBER | 监听入站 HTTP 连接的端口号 | 8080 |
| LOGSTASH_ENABLE_TCP_INPUT | 是否监听入站 TCP 连接 | no |
| LOGSTASH_TCP_PORT_NUMBER | 监听入站 TCP 连接的端口号 | 5010 |
| LOGSTASH_ENABLE_UDP_INPUT | 是否监听入站 UDP 连接 | no |
| LOGSTASH_UDP_PORT_NUMBER | 监听入站 UDP 连接的端口号 | 5000 |
| LOGSTASH_ENABLE_STDOUT_OUTPUT | 是否输出到标准输出 | yes |
| LOGSTASH_ENABLE_ELASTICSEARCH_OUTPUT | 是否输出到 Elasticsearch 服务器 | no |
| LOGSTASH_ELASTICSEARCH_HOST | Elasticsearch 服务器主机名 | elasticsearch |
| LOGSTASH_ELASTICSEARCH_PORT_NUMBER | Elasticsearch 服务器端口 | 9200 |

### 只读环境变量

| 名称 | 描述 | 默认值 |
| --- | --- | --- |
| LOGSTASH_BASE_DIR | Logstash 安装目录 | /opt/bitnami/logstash |
| LOGSTASH_CONF_DIR | Logstash 设置文件目录 | ${LOGSTASH_BASE_DIR}/config |
| LOGSTASH_DEFAULT_CONF_DIR | Logstash 默认设置文件目录 | ${LOGSTASH_BASE_DIR}/config.default |
| LOGSTASH_PIPELINE_CONF_DIR | Logstash 管道配置文件目录 | ${LOGSTASH_BASE_DIR}/pipeline |
| LOGSTASH_DEFAULT_PIPELINE_CONF_DIR | Logstash 默认管道配置文件目录 | ${LOGSTASH_BASE_DIR}/pipeline.default |
| LOGSTASH_BIN_DIR | Logstash 可执行文件目录 | ${LOGSTASH_BASE_DIR}/bin |
| LOGSTASH_CONF_FILE | Logstash 设置文件路径 | ${LOGSTASH_CONF_DIR}/logstash.yml |
| LOGSTASH_PIPELINE_CONF_FILE | Logstash 管道配置文件路径 | ${LOGSTASH_PIPELINE_CONF_DIR}/${LOGSTASH_PIPELINE_CONF_FILENAME} |
| LOGSTASH_VOLUME_DIR | 持久化基础目录 | /bitnami/logstash |
| LOGSTASH_DATA_DIR | Logstash 数据目录 | ${LOGSTASH_VOLUME_DIR}/data |
| LOGSTASH_MOUNTED_CONF_DIR | Logstash 设置文件将被挂载的目录 | ${LOGSTASH_VOLUME_DIR}/config |
| LOGSTASH_MOUNTED_PIPELINE_CONF_DIR | Logstash 管道配置文件将被挂载的目录 | ${LOGSTASH_VOLUME_DIR}/pipeline |
| LOGSTASH_DAEMON_USER | Logstash 系统用户 | logstash |
| LOGSTASH_DAEMON_GROUP | Logstash 系统组 | logstash |
| JAVA_HOME | Java 安装文件夹 | ${BITNAMI_ROOT_DIR}/java |
