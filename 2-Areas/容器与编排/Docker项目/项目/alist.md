# Alist 文件列表程序

支持多种存储的文件列表程序，可统一管理本地存储、阿里云盘、OneDrive、S3 等多种网盘和存储服务。

## 部署

```yaml
version: '3.8'
services:
    alist:
        container_name: alist
        hostname: alist
        image: xhofe/alist:v3.29.1
        restart: always
        volumes:
            - './local-data:/local-data'
            - './config:/opt/alist/data'
            - '/etc/localtime:/etc/localtime:ro'
        ports:
            - '5244:5244'
            - '5426:5426'
        environment:
            - PUID=0
            - PGID=0
            - UMASK=022
```

## 说明

- 访问地址：`http://<IP>:5244`
- 端口映射：`5244`（Web 界面）、`5426`（附加端口）
- `PUID` / `PGID`：运行用户的 UID 和 GID（0 为 root）
- `UMASK`：文件权限掩码
- 数据持久化：`./local-data`（本地存储）、`./config`（配置文件）
