# 容器化部署KodExplorer资源管理器

容器化部署KodExplorer资源管理器相关技术笔记。



容器化部署 Explorer 资源管理器

1. **案例目标**
    1. 掌握Dcokerfile 的基本语法。
    2. 掌握常用中间件的容器化方法。
    3. 了解Explorer 资源管理系统的容器化部署。

1. **案例准备**

# 规划节点

节点规划，见表 2-1-1。

表 2-1-1 节点规划

| **IP** | **主机名** | **节点** |
| :---: | --- | :---: |
| 10.24.2.63 | master | Kubernetes ALL_IN_ONE 节点 |

# 基础准备

Docker 和Docker Compose 已安装完成。

1. **案例实施**

# Explorer 资源管理器介绍

Explorer 是一个开源的 Web 文件管理器，提供了在线文件管理、文件预览、编辑、上传和下载等功能。也可以叫它网盘，但是它不具备网盘的很多功能。

它采用了前端技术，无需安装任何软件，通过浏览器即可访问和使用，并且支持多用户、多平台、多语言、多种文件格式的在线浏览和编辑。

还提供了插件机制，可以扩展更多的功能，如图像在线处理、压缩、解压缩等。

# 容器化部署 MariaDB

        1. **基础环境准备**上传软件包并解压：

[root@k8s-master-node1 ~]# tar -zxvf Explorer.tar.gz

导入CentOS 基础镜像：

[root@k8s-master-node1 ~]# docker load -i KodExplorer/CentOS_7.9.2009.tar

# 编写 Dockerfile
编写 init.sh 脚本：

[root@k8s-worker-node1 ~]# cd KodExplorer/ [root@k8s-worker-node1 KodExplorer]# vi mysql_init.sh #!/bin/bash

mysql_install_db --user=root mysqld_safe --user=root & sleep 8

mysqladmin -u root password 'root'

mysql -uroot -proot -e "grant all on *.* to 'root'@'%' identified by 'root'; flush privileges;"

编写 yum 源：

[root@k8s-worker-node1 KodExplorer]# vi local.repo [yum]

name=yum baseurl=file:///root/yum gpgcheck=0

enabled=1

编写Dockerfile 文件：

[root@k8s-worker-node1 KodExplorer]# vi Dockerfile-mariadb FROM centos:centos7.9.2009

MAINTAINER Chinaskills RUN rm -rfv /etc/yum.repos.d/*

COPY local.repo /etc/yum.repos.d/

COPY yum /root/yum

ENV LC_ALL en_US.UTF-8

RUN yum -y install mariadb-server COPY mysql_init.sh /opt/

RUN bash /opt/mysql_init.sh EXPOSE 3306

CMD ["mysqld_safe","--user=root"]

        1. **构建镜像**构建镜像：

[root@k8s-worker-node1	KodExplorer]#	docker	build	-t	kod-mysql:v1.0	-f

Dockerfile-mariadb .

[+] Building 56.6s (8/11)                                   docker:default

[+] Building 104.7s (12/12) FINISHED                        docker:default

 => [internal] load .dockerignore	0.2s

 => => transferring context: 2B	0.0s

 => [internal] load build definition from Dockerfile-mariadb	0.2s

 => => transferring dockerfile: 397B	0.0s

 => [internal] load metadata for docker.io/library/centos:centos7.9.2009 0.0s

 => CACHED [1/7] FROM docker.io/library/centos:centos7.9.2009	0.0s

 => [internal] load build context	10.8s

 => => transferring context: 350.48MB	3.4s

 => [2/7] RUN rm -rfv /etc/yum.repos.d/*	10.9s

 => [3/7] COPY local.repo /etc/yum.repos.d/	2.8s

 => [4/7] COPY yum /root/yum	14.5s

 => [5/7] RUN yum -y install mariadb-server	55.3s

 => [6/7] COPY mysql_init.sh /opt/	0.2s

 => [7/7] RUN bash /opt/mysql_init.sh	16.1s

 => exporting to image	4.5s

 => => exporting layers	4.4s

 => => writing image

sha256:d7b697a36449c5bbcd382d4e260d4a5b4e559985dfd5aca76b73bd0823cda7df	0.0s

 => => naming to docker.io/library/kod-mysql:v1.0	0.0s

# 容器化部署 Redis

1. **编写 Dockerfile**

编写Dockerfile 文件：

[root@k8s-worker-node1 KodExplorer]# vi Dockerfile-redis FROM centos:centos7.9.2009

MAINTAINER Chinaskills RUN rm -rf /etc/yum.repos.d/*

COPY local.repo /etc/yum.repos.d/ COPY yum /root/yum

RUN yum -y install redis

RUN sed -i 's/127.0.0.1/0.0.0.0/g' /etc/redis.conf && \

sed -i 's/protected-mode yes/protected-mode no/g' /etc/redis.conf EXPOSE 6379

CMD ["/usr/bin/redis-server","/etc/redis.conf"]

# 构建镜像

[root@k8s-worker-node1 KodExplorer]# docker build -t kod-redis:v1.0 -f Dockerfile-redis .

[+] Building 27.1s (11/11) FINISHED                         docker:default

 => [internal] load .dockerignore	0.0s

 => => transferring context: 2B	0.0s

 => [internal] load build definition from Dockerfile-redis	0.0s

 => => transferring dockerfile: 449B	0.0s

 => [internal] load metadata for docker.io/library/centos:centos7.9.2009 0.0s

 => [1/6] FROM docker.io/library/centos:centos7.9.2009	0.0s

 => [internal] load build context	0.0s

 => => transferring context: 40.81kB	0.0s

 => CACHED [2/6] RUN rm -rf /etc/yum.repos.d/*	0.0s

 => [3/6] COPY local.repo /etc/yum.repos.d/	0.2s

 => [4/6] COPY yum /root/yum	14.1s

 => [5/6] RUN yum -y install redis	7.3s

 => [6/6] RUN sed -i 's/127.0.0.1/0.0.0.0/g' /etc/redis.conf && sed i

's/protected-mode yes/protected-mode no/g' /etc/redis.conf	0.7s

 => exporting to image	4.5s

 => => exporting layers	4.5s

 => => writing image sha256:7aa1768dfe2ce212982778da9d2c872d3d691a1a0f10cb23fcc9c7eae7dc6f44	0.0s

 => => naming to docker.io/library/kod-redis:v1.0	0.0s

    1. **容器化部署 PHP**

1. **编写 Dockerfile**

编写Dockerfile 文件：

[root@k8s-worker-node1 KodExplorer]# vi Dockerfile-php FROM centos:centos7.9.2009

MAINTAINER Chinaskills RUN rm -rf /etc/yum.repos.d/*

COPY local.repo /etc/yum.repos.d/ COPY yum /root/yum

RUN yum install httpd php php-cli unzip php-gd php-mbstring -y WORKDIR /var/www/html

COPY php/kodexplorer4.37.zip . RUN unzip kodexplorer4.37.zip RUN chmod -R 777 /var/www/html

RUN	sed	-i	's/#ServerName	www.example.com:80/ServerName	localhost:80/g'

/etc/httpd/conf/httpd.conf EXPOSE 80

CMD ["/usr/sbin/httpd","-D","FOREGROUND"]

# 构建镜像
[root@k8s-worker-node1 KodExplorer]# docker build -t kod-php:v1.0 -f Dockerfile-php .

[+] Building 54.2s (15/15) FINISHED

docker:default

 => [internal] load build definition from Dockerfile-php	0.0s

 => => transferring dockerfile: 565B	0.0s

 => [internal] load .dockerignore	0.0s

 => => transferring context: 2B	0.0s

 => [internal] load metadata for docker.io/library/centos:centos7.9.2009 0.0s

 => [ 1/10] FROM docker.io/library/centos:centos7.9.2009	0.0s

 => [internal] load build context	0.2s

 => => transferring context: 13.89MB	0.2s

 => CACHED [ 2/10] RUN rm -rf /etc/yum.repos.d/*	0.0s

 => CACHED [ 3/10] COPY local.repo /etc/yum.repos.d/	0.0s

 => CACHED [ 4/10] COPY yum /root/yum	0.0s

 => [ 5/10] RUN yum install httpd php php-cli unzip php-gd php-mbstring -y 39.4s

 => [ 6/10] WORKDIR /var/www/html	0.2s

 => [ 7/10] COPY php/kodexplorer4.37.zip	0.5s

 => [ 8/10] RUN unzip kodexplorer4.37.zip	2.8s

 => [ 9/10] RUN chmod -R 777 /var/www/html	8.5s

 => [10/10] RUN sed -i 's/#ServerName www.example.com:80/ServerName

localhost:80/g' /etc/httpd/conf/httpd.conf	0.9s

 => exporting to image	1.5s

 => => exporting layers	1.5s

 => => writing image

sha256:c3c6f684f8b15691ed391a3dc0474a03d3eb222252e8e4aeee116f732e948504	0.0s

 => => naming to docker.io/library/kod-php:v1.0	0.0s

# 容器化部署 Nginx

1. **编写 Dockerfile**

编写 dockerfile:

[root@k8s-worker-node1 KodExplorer]# vi Dockerfile-nginx FROM centos:centos7.9.2009

MAINTAINER Chinaskills RUN rm -rf /etc/yum.repos.d/*

COPY local.repo /etc/yum.repos.d/ COPY yum /root/yum

RUN yum -y install nginx RUN /bin/bash -c 'echo init ok' EXPOSE 80

CMD ["nginx","-g","daemon off;"]

# 构建镜像

[root@k8s-worker-node1	KodExplorer]#	docker	build	-t	kod-nginx:v1.0	-f Dockerfile-nginx .

[+] Building 16.0s (11/11) FINISHED                         docker:default

 => [internal] load .dockerignore	0.0s

 => => transferring context: 2B	0.0s

 => [internal] load build definition from Dockerfile-nginx	0.0s

 => => transferring dockerfile: 338B	0.0s

 => [internal] load metadata for docker.io/library/centos:centos7.9.2009 0.0s

 => [1/6] FROM docker.io/library/centos:centos7.9.2009	0.0s

 => [internal] load build context	0.1s

 => => transferring context: 40.81kB	0.0s

 => CACHED [2/6] RUN rm -rf /etc/yum.repos.d/*	0.0s

 => CACHED [3/6] COPY local.repo /etc/yum.repos.d/	0.0s

 => CACHED [4/6] COPY yum /root/yum	0.0s

 => [5/6] RUN yum -y install nginx	14.2s

 => [6/6] RUN /bin/bash -c 'echo init ok'	0.7s

 => exporting to image	0.8s

 => => exporting layers	0.8s

 => => writing image sha256:1c904f24ba2ca44f5014bf92920b4728694b34b2239974f9a5a42015cc5a4201	0.0s

 => => naming to docker.io/library/kod-nginx:v1.0	0.0s

    1. **编排部署服务**

1. **编写 docker-compose.yaml**

[root@k8s-worker-node1 KodExplorer]# vi docker-compose.yaml version: '3.2'

services:

nginx:

container_name: nginx image: kod-nginx:v1.0 volumes:

+ ./www:/data/www

+ ./nginx/logs:/var/log/nginx

ports:

- "443:443"

restart: always depends_on:

+ php-fpm

links:

+ php-fpm

tty: true

mysql:

container_name: mysql

image: kod-mysql:v1.0

volumes:

+ ./data/mysql:/var/lib/mysql

+ ./mysql/logs:/var/lib/mysql-logs

ports:

- "3306:3306"

restart: always

redis:

container_name: redis

image:	kod-redis:v1.0

ports:

- "6379:6379"

volumes:

    - ./data/redis:/data

    - ./redis/redis.conf:/usr/local/etc/redis/redis.conf

restart: always

command: redis-server /usr/local/etc/redis/redis.conf

php-fpm:

container_name: php-fpm

image: kod-php:v1.0

ports:

- "8090:80"

links:

    - mysql

    - redis

restart: always

depends_on:

        * redis

        * mysql

# 部署服务
[root@k8s-worker-node1 KodExplorer]# docker-compose up -d

Creating network "kodexplorer_default" with the default driver Creating redis	done

Creating mysql	done

Creating php-fpm	done

Creating nginx	done

查看服务：

|  | [root@k8s-worker-node1 KodExplorer]# docker-compose ps
Name Command State Ports  |
| --- | --- |
|  | |
|
mysql mysqld_safe --user=root Up 0.0.0.0:3306->3306/tcp,:::3306->3306/tcp
nginx nginx -g daemon off; Up 0.0.0.0:443->443/tcp,:::443->443/tcp, 80/tcp
php-fpm /usr/sbin/httpd -D FOREGROUND Up 0.0.0.0:8090->80/tcp,:::8090->80/tcp
redis    redis-server /usr/local/et	Up
0.0.0.0:6379->6379/tcp,:::6379->6379/tcp      | |

在浏览器上通过 http://IP:8090 访问 KodExplorer，如图所示：

![[1697447320962-b53d5472-74d3-4afe-9a05-f74137fa89ed.jpeg]]

![[1697447321332-3a5e567a-4a6a-4b02-8790-58b87a1c0f8a.jpeg]]设置管理员登录密码，点击登录。如图所示：

登录成功后如图所示：

![[1697447321644-1d3db7c5-deb4-4841-b663-304f47a1df39.jpeg]]
