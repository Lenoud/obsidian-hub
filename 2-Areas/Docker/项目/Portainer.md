# Portainer Docker 管理平台

轻量级的 Docker 环境管理 Web 界面，提供容器、镜像、网络和卷的可视化管理。

## 部署

```yaml
version: "3"
services:
  portainer:
    image: portainer/portainer-ce:2.21.3
    container_name: portainer
    ports:
      - 9000:9000
    network_mode: db_bridge
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./data:/data
      - /etc/localtime:/etc/localtime:ro
    restart: always
networks: {}
```

## 说明

- 访问地址：`http://<IP>:9000`
- 端口映射：`9000` -> `9000`
- 挂载 Docker socket：`/var/run/docker.sock` 用于管理宿主机 Docker
- 数据持久化：`./data` 映射到 `/data`
- `network_mode: db_bridge`：使用自定义网络
