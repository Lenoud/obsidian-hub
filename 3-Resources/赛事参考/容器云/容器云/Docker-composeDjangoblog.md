# Docker Compose 部署 DjangoBlog

使用 Docker Compose 编排 DjangoBlog 博客系统，包含 Memcached、MySQL、Nginx 和 Django 应用。

## 编排 memcached
```dockerfile
FROM centos:centos7.9.2009
RUN rm -rf /etc/yum.repos.d/*
COPY yum /root/yum
COPY yum.repo /etc/yum.repos.d/
RUN yum -y install libevent libevent-devel
RUN yum -y install memcached
EXPOSE 11211
CMD ["memcached","-u","root"]
```

# 编排数据库
```dockerfile
FROM centos:centos7.9.2009
RUN rm -rf /etc/yum.repos.d/*
COPY yum /root/yum
COPY yum.repo /etc/yum.repos.d/
RUN yum -y install mariadb mariadb-server
COPY init.sh /opt/
COPY sqlfile.sql /opt/
RUN chmod 777 /opt/init.sh
RUN bash /opt/init.sh
EXPOSE 3306
CMD ["mysqld_safe","--user=root"]

#/bin/bash
mysql_db_install --user=root
mysqld_safe --user=root &
sleep 8
mysqladmin -uroot password 'root'
mysql -uroot -proot -e "grant all on *.* to 'root'@'%' identified by 'root';"
mysql -uroot -proot -e 'create database djangoblog;use djangoblog;source /opt/sqlfile.sql;'

```

# 编排前端nginx
```dockerfile
FROM centos:centos7.9.2009
RUN rm -rf /etc/yum.repos.d/*
COPY yum /root/yum
COPY yum.repo /etc/yum.repos.d/
RUN yum -y install nginx
COPY nginx.conf /etc/nginx/
EXPOSE 80
CMD ["nginx","-g","daemon off;"]
```

# 编排djangoblog
```dockerfile
FROM centos:centos7.9.2009
RUN rm -rf /etc/yum.repos.d/*
COPY yum /root/yum
COPY yum.repo /etc/yum.repos.d/

RUN yum -y install MariaDB-devel.x86_64 bzip2-devel.x86_64 gcc make expat-devel.x86_64 readline-devel.x86_64 sqlite-devel.x86_64  openssl-devel.x86_64 python-devel.x86_64 gdbm-devel.x86_64
ADD Python-3.6.5.tgz /root/
RUN cd /root/Python-3.6.5 && ./configure && make && make install
COPY Python-pip /opt/Python-pip
COPY requirements.txt /opt/
RUN pip3 install --upgrade pip --no-index --find-links /opt/Python-pip
RUN pip3 install -r /opt/requirements.txt --no-index --find-links /opt/Python-pip
RUN pip3 install gunicorn[gevent] --no-index --find-links=/opt/Python-pip
RUN mkdir -p /code/djangoBlog/
ADD . /code/djangoBlog/
RUN chmod 777  /code/djangoBlog/bin/docker_start.sh

EXPOSE 8000
ENTRYPOINT ["/code/djangoBlog/bin/docker_start.sh"]
```

# 编排docker-compose
```yaml
version: '3.0'
services:
  blog-memcached:
    image: blog-memcached:v1.0
    ports:
    - 11211:11211
    container_name: blog-memcached

  blog-nginx:
    image: blog-nginx:v1.0
    ports:
    - 8888:80
    container_name: blog-nginx
    volumes:
    - ./collectedstatic/:/code/djangoblog/collectedstatic/
    links:
    - blog-djangoblog:djangoblog

  blog-djangoblog:
    image: blog-server:v1.0
    ports:
    - 8000:8000
    container_name: blog-service
    volumes:
    - ./collectedstatic/:/code/djangobBlog/collectedstatic/
    environment:
    - DJANGO_MYSQL_HOST=mysql
    - DJANGO_MYSQL_DATABASE=djangoblog
    - DJANGO_MYSQL_USER=root
    - DJANGO_MYSQL_PASSWORD=root
    - DJANGO_MYSQL_PORT=3306
    - DJANGO_MEMCACHED_LOCATION=memcached:11211
    links:
    - blog-memcached:memcached
    - blog-mysql:mysql

  blog-mysql:
    image: blog-mysql:v1.0
    ports:
    - 3306:3306
    container_name: blog-mysql
```

# 记忆重点
```bash
  Django:
     environment:
    - DJANGO_MYSQL_HOST=mysql
    - DJANGO_MYSQL_DATABASE=djangoblog
    - DJANGO_MYSQL_USER=root
    - DJANGO_MYSQL_PASSWORD=root
    - DJANGO_MYSQL_PORT=3306
    - DJANGO_MEMCACHED_LOCATION=memcached:11211

[root@k8s-master-node1 DjangoBlog]# grep -r "DJANGO_MYSQL_" ./
./djangoblog/settings.py 这个文件里面有MYSQL相关的环境变量，重点记忆
DJANGO_MEMCACHED_LOCATION=

RUN yum -y install MariaDB-devel.x86_64 bzip2-devel.x86_64 gcc make expat-devel.x86_64 readline-devel.x86_64 sqlite-devel.x86_64  openssl-devel.x86_64 python-devel.x86_64 gdbm-devel.x86_64
ADD Python-3.6.5.tgz /root/
RUN cd /root/Python-3.6.5 && ./configure && make && make install
COPY Python-pip /opt/Python-pip
COPY requirements.txt /opt/
RUN pip3 install --upgrade pip --no-index --find-links /opt/Python-pip
RUN pip3 install -r /opt/requirements.txt --no-index --find-links /opt/Python-pip
RUN pip3 install gunicorn[gevent] --no-index --find-links=/opt/Python-pip
RUN mkdir -p /code/djangoBlog/
ADD . /code/djangoBlog/
RUN chmod 777  /code/djangoBlog/bin/docker_start.sh
```
