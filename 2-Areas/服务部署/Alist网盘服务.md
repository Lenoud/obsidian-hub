# Alist网盘服务

Alist网盘服务相关技术笔记。

[Home](https://alist.nn.ci/zh/)

# 使用 Docker部署
```bash
#部署服务
docker run -d --restart=always -v /opt/ShareFile:/opt/filedata -v /opt/alist/config:/opt/alist/data \
-p 5244:5244 -e PUID=0 -e PGID=0 -e UMASK=022 --name="alist" xhofe/alist:latest

#查看管理用户账号密码
docker exec -it alist ./alist admin
```

#PUID和PGID的环境变量是用户的uid和gid这是写的是root用户的ID：0

#查看版本信息

docker exec -it alist ./alist version

#更新alist

docker ps -a #查看容器(找Alist容器的ID)

docker stop ID #停止Alist运行,不然无法删除

docker rm ID #删除Alist容器(数据还在只要你不手动删除)

docker pull xhofe/alist:latest

#重新docker run alist即可

# docker-compose部署
```bash
version: '3.8'
services:
    alist:
        restart: always
        volumes:
            - './filedata:/filedata'
            - './config:/opt/alist/data'
            - '/mnt/smb-200-share02:/mnt/openfiler_4T'
            - '/mnt/smb-201-share02:/mnt/truenas_5T'
            - '/mnt/smb-201-share01:/mnt/truenas_1T'
        ports:
            - '5244:5244'
        environment:
            - PUID=0
            - PGID=0
            - UMASK=022
        container_name: alist
        image: xhofe/alist:v3.29.1

```

```yaml
version: '3.3'
services:
    alist:
        restart: always
        volumes:
            - '/opt/ShareFile:/opt/filedata'   	 #共享数据存放路径
            - '/opt/alist/config:/opt/alist/data' #配置文件存放路径
        ports:
            - '5244:5244'
        environment:
            - PUID=0
            - PGID=0
            - UMASK=022
        container_name: alist
        image: 'xhofe/alist:latest'

```

#检查文件是否错误

docker-compose config

#启动alist

docker-compose up -d

#查看服务状态

docker-compose ps

#管理员信息

docker-compose exec -it alist ./alist admin

#docker-compose更新alist

docker-compose pull

docker-compose up -d
