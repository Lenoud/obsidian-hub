# docker-compose Managr

docker-compose Managr相关技术笔记。

## 启动一个docker-compose 的 web Manager面板
```yaml
services:
  dockge:
    image: louislam/dockge:1.4.2
    container_name: dockge
    hostname: dockge
    restart: unless-stopped
    ports:
      - 5001:5001
    network_mode: bridge
    volumes:
      - ./data:/app/data
      - /var/run/docker.sock:/var/run/docker.sock
      - /root/:/root
      #- ./stacks:/opt/stacks
    environment:
      #- DOCKGE_STACKS_DIR=/opt/stacks
      - DOCKGE_STACKS_DIR=/root
    labels:
      createdBy: Apps
```
