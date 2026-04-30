# docker+gogs+drone

docker+gogs+drone相关技术笔记。

```yaml
version: '3'
services:
  drone-server:
    image: drone/drone:latest
    ports:
      # Web管理面板的入口，当PROTO=http时使用该端口
      - "8081:80"
      # Web管理面板的入口，当PROTO=https时使用该端口
      - 8843:443
    volumes:
      # Drone数据存储
      - drone_data:/data
    environment:
      #启用Drone Runner，如果不启用，则Drone Server将会作为默认的Runner
      - DRONE_AGENTS_ENABLED=true
      # 开启注册，此配置允许任何人自行注册和登录系统
      - DRONE_OPEN=true
      # 服务器主机名，用于创建Webhook和重定向URL
      - DRONE_SERVER_HOST=drone-server
      # 协议，用于创建Webhook和重定向URL，默认为HTTPS（需要SSL支持），这里建议用HTTP
      - DRONE_SERVER_PROTO=http
      # Gogs服务器
      - DRONE_GOGS_SERVER=http://gogs:3000
      # 复制公共仓库时是否认证，只有代码管理系统启用私有模式才有意义
      - DRONE_GIT_ALWAYS_AUTH=false
      # RPC密钥，服务器与Runner必须相同
      - DRONE_RPC_SECRET=abc123456

  drone-agent:
    image: drone/agent:latest
    depends_on:
      - drone-server
    environment:
      # Drone服务器地址
      - DRONE_RPC_SERVER=http://drone-server
      # 连接Drone服务器的协议
      - DRONE_RPC_PROTO=http
      # RPC密钥，Runner与服务器必须相同
      - DRONE_RPC_SECRET=abc123456
      # 最大并发执行的流水线数
      - DRONE_MAX_PROCS=5
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock

  gogs:
    image: gogs/gogs:latest
    ports:
      - "10022:22"
      - "3000:3000"
    volumes:
      - gogs_data:/data
    depends_on:
      - mysql

  mysql:
    image: mysql:5.7
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - db_data:/var/lib/mysql
    command: --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: gogs
      MYSQL_USER: gogs
      MYSQL_PASSWORD: gogs
      TZ: Asia/Shanghai

  registry:
    image: registry
    volumes:
      - registry_data:/var/lib/registry
    ports:
      - 5000:5000

volumes:
    drone_data: {}
    db_data: {}
    gogs_data: {}
    registry_data: {}

```
