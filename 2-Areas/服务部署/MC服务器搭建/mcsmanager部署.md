# docker形式

docker形式相关技术笔记。
### 1.mcsmanager-daemon后端
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

### 2.mcsmanager-web前端
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
