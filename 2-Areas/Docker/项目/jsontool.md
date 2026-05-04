# JSON Hero JSON 查看工具

在线 JSON 数据查看和分析工具，提供树形结构、代码高亮和路径浏览等功能。

## 部署

```yaml
services:
  jsonhero:
    container_name: jsonhero
    image: henryclw/jsonhero-web:latest
    restart: always
    network_mode: bridge
    ports:
      - 893:8787
    labels:
      createdBy: Apps
    volumes:
      - /etc/localtime:/etc/localtime:ro
networks: {}
```

## 说明

- 访问地址：`http://<IP>:893`
- 端口映射：`893` -> `8787`
