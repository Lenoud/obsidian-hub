# Tomcat 8 (Bitnami) Java 应用服务器

基于 Bitnami 镜像的 Apache Tomcat 8.5 Java Servlet 容器，用于部署和运行 Java Web 应用。

## 部署

### compose.yaml

```yaml
version: "3.8"
services:
  tomcat8:
    image: bitnami/tomcat:8.5.100
    container_name: tomcat8
    hostname: tomcat8
    network_mode: db_bridge
    environment:
      TOMCAT_SHUTDOWN_PORT_NUMBER: $TOMCAT_SHUTDOWN_PORT_NUMBER
      TOMCAT_HTTP_PORT_NUMBER: $TOMCAT_HTTP_PORT_NUMBER
      TOMCAT_AJP_PORT_NUMBER: $TOMCAT_AJP_PORT_NUMBER
      TOMCAT_USERNAME: $TOMCAT_USERNAME
      TOMCAT_PASSWORD: $TOMCAT_PASSWORD
      TOMCAT_ALLOW_REMOTE_MANAGEMENT: $TOMCAT_ALLOW_REMOTE_MANAGEMENT
      TOMCAT_INSTALL_DEFAULT_WEBAPPS: $TOMCAT_INSTALL_DEFAULT_WEBAPPS
    ports:
      - 18080:8080
      - 8005:8005
    volumes:
      - ./webapps/:$TOMCAT_WEBAPPS_DIR/
    restart: unless-stopped
networks: {}
```

### .env 配置

```ini
TOMCAT_USERNAME=root
TOMCAT_PASSWORD=000000
TOMCAT_HTTP_PORT_NUMBER=8080
TOMCAT_SHUTDOWN_PORT_NUMBER=8005
TOMCAT_ALLOW_REMOTE_MANAGEMENT=yes
TOMCAT_AJP_PORT_NUMBER=8009
TOMCAT_WEBAPPS_DIR=/bitnami/tomcat/webapps/
#是否添加默认TOMCATweb
TOMCAT_INSTALL_DEFAULT_WEBAPPS=no
```

## 说明

- 访问地址：`http://<IP>:18080`
- 端口映射：`18080` -> `8080`（HTTP）、`8005` -> `8005`（Shutdown）
- `TOMCAT_USERNAME` / `TOMCAT_PASSWORD`：管理界面登录凭据
- `TOMCAT_ALLOW_REMOTE_MANAGEMENT`：允许远程管理
- `TOMCAT_INSTALL_DEFAULT_WEBAPPS`：是否安装默认 Web 应用
- 应用部署目录：`./webapps/` 映射到 `/bitnami/tomcat/webapps/`
- `network_mode: db_bridge`：使用自定义网络

### 环境变量参考

#### 可配置环境变量

| 名称 | 描述 | 默认值 |
| --- | --- | --- |
| TOMCAT_SHUTDOWN_PORT_NUMBER | Tomcat 关闭端口号 | 8005 |
| TOMCAT_HTTP_PORT_NUMBER | Tomcat HTTP 端口号 | 8080 |
| TOMCAT_AJP_PORT_NUMBER | Tomcat AJP 端口号 | 8009 |
| TOMCAT_USERNAME | Tomcat 用户名 | manager |
| TOMCAT_PASSWORD | Tomcat 密码 | nil（空） |
| TOMCAT_ALLOW_REMOTE_MANAGEMENT | 是否允许从远程地址连接到 Tomcat 管理应用 | yes |
| TOMCAT_ENABLE_AUTH | 是否为 Tomcat 管理应用启用认证 | yes |
| TOMCAT_ENABLE_AJP | 是否启用 Tomcat AJP 连接器 | no |
| TOMCAT_START_RETRIES | 等待 Catalina 启动时的重试次数 | 12 |
| TOMCAT_EXTRA_JAVA_OPTS | Tomcat 的额外 Java 设置 | nil（空） |
| TOMCAT_INSTALL_DEFAULT_WEBAPPS | 是否添加默认的 webapps（ROOT, manager, host-manager等）用于部署 | yes |
| JAVA_OPTS | Java 运行时参数 | 包括多个参数，如 -Djava.awt.headless=true 等 |

#### 只读环境变量

| 名称 | 描述 | 默认值 |
| --- | --- | --- |
| TOMCAT_BASE_DIR | Tomcat 安装目录 | ${BITNAMI_ROOT_DIR}/tomcat |
| TOMCAT_VOLUME_DIR | Tomcat 持久化目录 | /bitnami/tomcat |
| TOMCAT_BIN_DIR | Tomcat 二进制文件目录 | ${TOMCAT_BASE_DIR}/bin |
| TOMCAT_LIB_DIR | Tomcat 库文件目录 | ${TOMCAT_BASE_DIR}/lib |
| TOMCAT_WORK_DIR | Tomcat 运行时文件目录 | ${TOMCAT_BASE_DIR}/work |
| TOMCAT_WEBAPPS_DIR | 存储 webapps 的 Tomcat 目录 | ${TOMCAT_VOLUME_DIR}/webapps |
| TOMCAT_CONF_DIR | Tomcat 配置目录 | ${TOMCAT_BASE_DIR}/conf |
| TOMCAT_DEFAULT_CONF_DIR | Tomcat 默认配置目录 | ${TOMCAT_BASE_DIR}/conf.default |
| TOMCAT_CONF_FILE | Tomcat 配置文件 | ${TOMCAT_CONF_DIR}/server.xml |
| TOMCAT_USERS_CONF_FILE | Tomcat 用户配置文件 | ${TOMCAT_CONF_DIR}/tomcat-users.xml |
| TOMCAT_LOGS_DIR | 存储 Tomcat 日志的目录 | ${TOMCAT_BASE_DIR}/logs |
| TOMCAT_TMP_DIR | 存储 Tomcat 临时文件的目录 | ${TOMCAT_BASE_DIR}/temp |
| TOMCAT_LOG_FILE | Tomcat 日志文件路径 | ${TOMCAT_LOGS_DIR}/catalina.out |
| TOMCAT_PID_FILE | Tomcat PID 文件路径 | ${TOMCAT_TMP_DIR}/catalina.pid |
| TOMCAT_HOME | Tomcat 家目录 | $TOMCAT_BASE_DIR |
| TOMCAT_DAEMON_USER | Tomcat 系统用户 | tomcat |
| TOMCAT_DAEMON_GROUP | Tomcat 系统组 | tomcat |
| JAVA_HOME | Java 安装文件夹 | ${BITNAMI_ROOT_DIR}/java |
