# MCSManager Minecraft 服务器管理面板

开源的 Minecraft 服务器管理面板，包含 Web 前端和 Daemon 后端，支持多实例管理和远程控制。

## 部署

### MCSManager-Daemon 后端

```yaml
version: "3.0"
services:
  mcsmanager-daemon:
    image: alisaqaq/mcsmanager-daemon:3.4.0
    container_name: mcsmanager-daemon
    restart: always
    network_mode: host
    deploy:
      resources:
        limits:
          cpus: "0"
    volumes:
    - ./data:/opt/mcsmanager/daemon/data
    - /var/run/docker.sock:/var/run/docker.sock
    environment:
    #设置java环境变量java路径 容器内/opt/mcsmanager/daemon/data/
      - PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/mcsmanager/daemon/data/jdk-17/bin
      - JAVA_HOME=/opt/mcsmanager/daemon/data/jdk-17
      - NODE_VERSION=14.21.3
      - YARN_VERSION=1.22.19
      - TZ=Asia/Shanghai
```

### MCSManager-Web 前端

```yaml
version: "3.0"
services:
  mcsmanager-web:
    image: alisaqaq/mcsmanager-web:9.9.0
    container_name: mcsmanager-web
    network_mode: host
    restart: always
    deploy:
      resources:
        limits:
          cpus: "0"
    volumes:
    - ./data:/opt/mcsmanager/web/data
```

## 说明

- 两个服务均使用 `network_mode: host`，直接使用主机网络
- `JAVA_HOME`：Java 17 路径（需在 `./data` 目录中放入 JDK 17）
- `NODE_VERSION`：Node.js 版本
- `TZ`：时区设置为 `Asia/Shanghai`
- Daemon 数据持久化：`./data` 映射到 `/opt/mcsmanager/daemon/data`
- Web 数据持久化：`./data` 映射到 `/opt/mcsmanager/web/data`
- 挂载 Docker socket：`/var/run/docker.sock` 用于管理 Docker 容器
