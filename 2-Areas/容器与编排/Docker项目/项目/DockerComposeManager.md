# Dockge Docker Compose 管理器

基于 Web 的 Docker Compose 管理工具，提供可视化的 Compose 文件编辑和容器堆栈管理界面。

## 部署

```yaml
services:
  dockge:
    image: louislam/dockge:1.4.2
    container_name: dockge
    hostname: dockge
    restart: unless-stopped
    user: root
    ports:
      - 5001:5001
    network_mode: bridge
    volumes:
      - ./data:/app/data
      - /var/run/docker.sock:/var/run/docker.sock
      - /root/:/root #- ./stacks:/opt/stacks
      - /etc/localtime:/etc/localtime:ro
    environment:
      #- DOCKGE_STACKS_DIR=/opt/stacks
      - DOCKGE_STACKS_DIR=/root
    labels:
      createdBy: Apps
```

## 说明

- 访问地址：`http://<IP>:5001`
- `DOCKGE_STACKS_DIR`：Compose 堆栈文件存放目录
- 挂载 Docker socket：`/var/run/docker.sock` 用于管理 Docker
- 数据持久化：`./data` 映射到 `/app/data`
