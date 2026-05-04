# Nginx Web 服务器

高性能的 HTTP 和反向代理服务器，广泛用于静态资源服务、负载均衡和 SSL 终端。

## 准备配置文件

先准备好标准配置文件：

```bash
mkdir  ssl
docker run -id  --rm --name n1 nginx:1.25

docker cp n1:/etc/nginx/conf.d/ ./conf.d/
docker cp n1:/usr/share/nginx/html/ ./html/

docker rm -f n1
```

## 部署

```yaml
version: '3.0'
services:
  nginx:
    container_name: nginx1
    hostname: nginx1
    image: nginx:1.25
    restart: always
    network_mode: host
    deploy:
      resources:
        limits:
          cpus: "1.00"
          memory: 300M
    volumes:
    - /etc/localtime:/etc/localtime:ro
    - ./conf.d/:/etc/nginx/conf.d/ #配置文件存放位置
    - ./html/:/usr/share/nginx/html/  #资源文件存放位置
    - ./logs/:/var/log/nginx/ #日志存放位置
    - ./ssl/:/etc/nginx/ssl/ #证书文件存放位置
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

## 说明

- 使用 `network_mode: host`，直接使用主机网络
- 资源限制：1 核 CPU、300M 内存
- 配置文件：`./conf.d/` 映射到 `/etc/nginx/conf.d/`
- 静态资源：`./html/` 映射到 `/usr/share/nginx/html/`
- 日志文件：`./logs/` 映射到 `/var/log/nginx/`
- SSL 证书：`./ssl/` 映射到 `/etc/nginx/ssl/`
- 日志轮转：最大 10M 单文件，保留 3 个文件

## 相关笔记

- [[docker部署nginx]]
- [[nginx默认目录结构]]
- [[MOC-服务部署]]
