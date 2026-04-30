# docker部署nginx

docker部署nginx相关技术笔记。

使用 Docker 容器化部署。



```yaml
version: '3.0'
services:
  nginx:
    image: nginx:latest
    container_name: nginx
    network_mode: host
    volumes:
    - /etc/localtime:/etc/localtime:ro
    - ./logs:/var/log/nginx/  #nginx日志文件
    - ./config:/etc/nginx/  #配置文件
    - ./html:/usr/share/nginx/html/  #网站根目录
```
