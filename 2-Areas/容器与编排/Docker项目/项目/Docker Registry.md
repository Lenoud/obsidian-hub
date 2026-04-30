# Docker Registry 私有镜像仓库

Docker 官方的私有镜像仓库，用于存储和分发 Docker 镜像，附带 Web 管理界面。

## 部署

```yaml
version: '3'
services:
  registry:
    image: registry:2.8.3
    restart: always
    container_name: registry
    ports:
      - 5000:5000
    volumes:
      - ./data:/var/lib/registry

  registry-web:
    image: hyper/docker-registry-web
    ports:
      - 8080:8080
    environment:
      - REGISTRY_URL=http://registry:5000/v2
      - REGISTRY_NAME=192.168.100.20:5000  #web显示的registry地址
    restart: always
```

## 说明

- Registry 端口：`5000`（镜像推送/拉取）
- Web 管理界面：`http://<IP>:8080`
- `REGISTRY_URL`：Web 界面连接 Registry 的内部地址
- `REGISTRY_NAME`：Web 界面显示的 Registry 外部地址
- 镜像数据持久化：`./data` 映射到 `/var/lib/registry`
