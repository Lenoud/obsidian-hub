# Homebox 内网测速

内网网络性能测试工具，通过 Web 界面进行上传/下载速度测试。

## 部署

```yaml
version: '3.8'

services:
  homebox:
    image: xgheaven/homebox:latest
    container_name: homebox
    hostname: homebox
    restart: always
    network_mode: bridge
    ports:
      - "3300:3300"  # 映射主机端口到容器端口
```

## 说明

- 访问地址：`http://<IP>:3300`
- 端口映射：`3300` -> `3300`

## 相关笔记

- [[MOC-网络技术]]
