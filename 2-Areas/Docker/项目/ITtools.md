# IT Tools 开发者工具集

面向 IT 从业者的在线工具集合，包含编码转换、加密解密、网络计算等多种实用工具。

## 部署

```yaml
version: "3.8"
services:
  it-tools:
    image: corentinth/it-tools:2023.12.21-5ed3693
    container_name: ITtool
    network_mode: bridge
    restart: unless-stopped
    ports:
      - 891:80
    volumes:
      - /etc/localtime:/etc/localtime:ro
networks: {}
```

## 说明

- 访问地址：`http://<IP>:891`
- 端口映射：`891` -> `80`
