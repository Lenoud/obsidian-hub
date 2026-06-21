# 配置Docker Registry管理界面

配置Docker Registry管理界面相关技术笔记。

### 配置Docker Registry管理界面
Docker官方只提供了REST API，并没有给我们一个界面。 可以使用Portus来管理私有仓库， 同时可以使用简单的UI管理工具， Docker提供私有库“hyper/docker-registry-web”， 下载该镜像就可以使用了。

参考 [https://hub.docker.com/r/hyper/docker-registry-web/](https://hub.docker.com/r/hyper/docker-registry-web/)

```bash
docker run -d \
       -p 8080:8080 \
       --name registry-web \
       --link docker-registry \
       -e REGISTRY_URL=http://docker-registry:5000/v2 \
       -e REGISTRY_NAME=localhost:5000 \
       hyper/docker-registry-web
```

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
