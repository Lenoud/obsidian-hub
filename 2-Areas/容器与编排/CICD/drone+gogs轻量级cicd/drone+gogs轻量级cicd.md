# drone+gogs轻量级cicd

drone+gogs轻量级cicd相关技术笔记。

## 基于docker
```shell
version: '3'
services:
  drone-server:
    image: drone/drone:latest
    ports:
      # Web管理面板的入口，当PROTO=http时使用该端口
      - "8080:80"
      # Web管理面板的入口，当PROTO=https时使用该端口
      - 8843:443
    volumes:
      # Drone数据存储
      - drone_data:/data
    environment:
      #设置管理员用户（gogs创建用户时也需要使用luobozi这个用户名）
      - DRONE_USER_CREATE=username:luobozi,admin:true
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

  drone-runner:
    image: drone/drone-runner-docker:1.6.3
    depends_on:
      - drone-server
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:rw
    environment:
      - DRONE_RPC_HOST=drone-server
      - DRONE_RPC_SECRET=abc123456
      - DRONE_RPC_PROTO=http
      - DRONE_RUNNER_CAPACITY=4
      - DRONE_RUNNER_NAME=runner
      - DRONE_RUNNER_LABELS=machine1:runner1
      #两个键值对作为 标识, 可以根据实际情况自己定义, 只要遵循 {Key1}:{Value1},{Key2}:{Value2}
      - DRONE_DEBUG=true
      - DRONE_LOGS_DEBUG=true
      - DRONE_LOGS_PRETTY=true
      - DRONE_LOGS_NOCOLOR=false
# DRONE_RPC_HOST：指定Drone服务器的地址和端口。
# DRONE_RPC_SECRET：指定与Drone服务器通信所需的密钥。
# DRONE_RPC_PROTO：指定通信协议，这里是HTTP协议。
# DRONE_RUNNER_CAPACITY：指定该运行器最多可以并发运行的任务数，这里是4。
# DRONE_RUNNER_NAME：指定该运行器的名称。
# DRONE_RUNNER_LABELS：指定该运行器的标签，可以用于对任务进行选择。
# DRONE_DEBUG：指定是否启用调试模式。
# DRONE_LOGS_DEBUG：指定是否启用日志调试模式。
# DRONE_LOGS_PRETTY：指定是否将日志输出为漂亮的格式。
# DRONE_LOGS_NOCOLOR：指定是否禁用日志颜色。

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

## 服务器部署案例
```yaml
version: '3'
services:
  drone-server:
    restart: always
    image: drone/drone:latest
    cpus: 0.5
    mem_limit: 100m
    ports:
      - 30080:80
      - 30443:443
    volumes:
      - drone_data:/data
    environment:
      - DRONE_USER_CREATE=username:luobozi,admin:true
      - DRONE_AGENTS_ENABLED=true
      - DRONE_OPEN=true
      - DRONE_SERVER_HOST=drone-server
      - DRONE_SERVER_PROTO=http
      - DRONE_GOGS_SERVER=http://gogs:3000
      - DRONE_GIT_ALWAYS_AUTH=false
      - DRONE_RPC_SECRET=abc123456
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.2

  drone-agent:
    restart: always
    image: drone/agent:latest
    cpus: 0.5
    mem_limit: 100m
    depends_on:
      - drone-server
    environment:
      - DRONE_RPC_SERVER=http://drone-server
      - DRONE_RPC_PROTO=http
      - DRONE_RPC_SECRET=abc123456
      - DRONE_MAX_PROCS=5
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.3

  drone-runner:
    restart: always
    image: drone/drone-runner-docker:1.6.3
    cpus: 0.5
    mem_limit: 100m
    depends_on:
      - drone-server
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:rw
    environment:
      - DRONE_RPC_HOST=drone-server
      - DRONE_RPC_SECRET=abc123456
      - DRONE_RPC_PROTO=http
      - DRONE_RUNNER_CAPACITY=4
      - DRONE_RUNNER_NAME=runner
      - DRONE_RUNNER_LABELS=machine1:runner1
      - DRONE_DEBUG=true
      - DRONE_LOGS_DEBUG=true
      - DRONE_LOGS_PRETTY=true
      - DRONE_LOGS_NOCOLOR=false
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.4

  gogs:
    restart: always
    image: gogs/gogs:latest
    cpus: 0.5
    mem_limit: 300m
    ports:
      - "30022:22"
      - "3000:3000"
    volumes:
      - gogs_data:/data
    depends_on:
      - mysql
    environment:
      - SKIP_VERIFY=true
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.5

  mysql:
    restart: always
    image: mysql:5.7
    cpus: 0.5
    mem_limit: 500m
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - gogs_db_data:/var/lib/mysql
    command: --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: gogs
      MYSQL_USER: gogs
      MYSQL_PASSWORD: gogs
      TZ: Asia/Shanghai
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.6

  registry:
    restart: always
    image: registry
    volumes:
      - registry_data:/var/lib/registry
    ports:
      - 5000:5000
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.7

volumes:
  drone_data: {}
  gogs_db_data: {}
  gogs_data: {}
  registry_data: {}

networks:
  gogs_bridge:
    driver: bridge
    ipam:
      config:
        - subnet: 172.22.0.0/16

```

## 使用固定版本
```yaml
version: '3'
services:
  drone-server:
    restart: always
    image: drone/drone:2.21.0
    cpus: 0.5
    mem_limit: 100m
    ports:
      - 30080:80
      - 30443:443
    volumes:
      - drone_data:/data
    environment:
      - DRONE_USER_CREATE=username:luobozi,admin:true
      - DRONE_AGENTS_ENABLED=true
      - DRONE_OPEN=true
      - DRONE_SERVER_HOST=drone-server
      - DRONE_SERVER_PROTO=http
      - DRONE_GOGS_SERVER=http://gogs:3000
      - DRONE_GIT_ALWAYS_AUTH=false
      - DRONE_RPC_SECRET=abc123456
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.2

  drone-agent:
    restart: always
    image: drone/agent:1.6.2
    cpus: 0.5
    mem_limit: 100m
    depends_on:
      - drone-server
    environment:
      - DRONE_RPC_SERVER=http://drone-server
      - DRONE_RPC_PROTO=http
      - DRONE_RPC_SECRET=abc123456
      - DRONE_MAX_PROCS=5
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.3

  drone-runner:
    restart: always
    image: drone/drone-runner-docker:1.6.3
    cpus: 0.5
    mem_limit: 100m
    depends_on:
      - drone-server
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:rw
    environment:
      - DRONE_RPC_HOST=drone-server
      - DRONE_RPC_SECRET=abc123456
      - DRONE_RPC_PROTO=http
      - DRONE_RUNNER_CAPACITY=4
      - DRONE_RUNNER_NAME=runner
      - DRONE_RUNNER_LABELS=machine1:runner1
      - DRONE_DEBUG=true
      - DRONE_LOGS_DEBUG=true
      - DRONE_LOGS_PRETTY=true
      - DRONE_LOGS_NOCOLOR=false
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.4

  gogs:
    restart: always
    image: gogs/gogs:0.13
    cpus: 0.5
    mem_limit: 300m
    ports:
      - "30022:22"
      - "3000:3000"
    volumes:
      - gogs_data:/data
    depends_on:
      - mysql
    environment:
      - SKIP_VERIFY=true
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.5

  mysql:
    restart: always
    image: mysql:5.7
    cpus: 0.5
    mem_limit: 500m
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - gogs_db_data:/var/lib/mysql
    command: --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: gogs
      MYSQL_USER: gogs
      MYSQL_PASSWORD: gogs
      TZ: Asia/Shanghai
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.6

  registry:
    restart: always
    image: registry:2
    cpus: 0.5
    mem_limit: 300m
    volumes:
      - registry_data:/var/lib/registry
    ports:
      - 5000:5000
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.7

  registry-web:
    cpus: 1
    mem_limit: 1024m
    image: hyper/docker-registry-web
    ports:
      - 5001:8080
    environment:
      - REGISTRY_URL=http://registry:5000/v2
      - REGISTRY_NAME=192.168.1.20:5000
    restart: always
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.8

volumes:
  drone_data: {}
  gogs_db_data: {}
  gogs_data: {}
  registry_data: {}

networks:
  gogs_bridge:
    driver: bridge
    ipam:
      config:
        - subnet: 172.22.0.0/16

```

## 绑定挂载
```yaml
version: '3'
services:
  drone-server:
    image: drone/drone:2.21.0
    cpus: 0.5
    mem_limit: 100m
    ports:
      - 30080:80
      - 30443:443
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./drone_data:/data
    environment:
      - DRONE_USER_CREATE=username:luobozi,admin:true
      - DRONE_AGENTS_ENABLED=true
      - DRONE_OPEN=true
      - DRONE_SERVER_HOST=drone-server
      - DRONE_SERVER_PROTO=http
      - DRONE_GOGS_SERVER=http://gogs:3000
      - DRONE_GIT_ALWAYS_AUTH=false
      - DRONE_RPC_SECRET=abc123456
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.2

  drone-agent:
    image: drone/agent:1.6.2
    cpus: 0.5
    mem_limit: 100m
    depends_on:
      - drone-server
    environment:
      - DRONE_RPC_SERVER=http://drone-server
      - DRONE_RPC_PROTO=http
      - DRONE_RPC_SECRET=abc123456
      - DRONE_MAX_PROCS=5
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.3

  drone-runner:
    image: drone/drone-runner-docker:1.6.3
    cpus: 0.5
    mem_limit: 100m
    depends_on:
      - drone-server
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock:rw
    environment:
      - DRONE_RPC_HOST=drone-server
      - DRONE_RPC_SECRET=abc123456
      - DRONE_RPC_PROTO=http
      - DRONE_RUNNER_CAPACITY=4
      - DRONE_RUNNER_NAME=runner
      - DRONE_RUNNER_LABELS=machine1:runner1
      - DRONE_DEBUG=true
      - DRONE_LOGS_DEBUG=true
      - DRONE_LOGS_PRETTY=true
      - DRONE_LOGS_NOCOLOR=false
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.4

  gogs:
    image: gogs/gogs:0.13
    cpus: 0.5
    mem_limit: 300m
    ports:
      - "30022:22"
      - "3000:3000"
    volumes:
      - ./data_gogs:/data
    depends_on:
      - mysql
    environment:
      - SKIP_VERIFY=true
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.5

  mysql:
    image: mysql:5.7
    cpus: 0.5
    mem_limit: 500m
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /etc/localtime:/etc/localtime:ro
      - ./mysql_data:/var/lib/mysql
    command: --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: gogs
      MYSQL_USER: gogs
      MYSQL_PASSWORD: gogs
      TZ: Asia/Shanghai
    networks:
      gogs_bridge:
        ipv4_address: 172.22.0.6

  # registry:
  #   image: registry
  #   volumes:
  #     - ./registry_data:/var/lib/registry
  #   ports:
  #     - 5000:5000

networks:
  gogs_bridge:
    driver: bridge
    ipam:
      config:
        - subnet: 172.22.0.0/16

```
