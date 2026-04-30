# Docker Compose 部署 ERP 系统

使用 Docker Compose 编排华夏 ERP 系统，包含 MySQL、Redis、Nginx 和 Java 应用。

## 编排数据库
```dockerfile
FROM centos:centos7.9.2009
RUN rm -rf /etc/yum.repos.d/*
COPY yum /root/yum
COPY local.repo /etc/yum.repos.d/
RUN yum -y install mariadb mariadb-server
COPY mysql_init.sh /opt/
ENV LC_ALL en_US.UTF-8
COPY jsh_erp.sql /opt/
RUN chmod 777 /opt/mysql_init.sh
RUN bash /opt/mysql_init.sh
CMD ["mysqld","--user=root"]

mysql_init.sh
#/bin/bash
mysql_db_install --user=root
mysqld_safe --user=root &
sleep 6
mysqladmin -uroot password 'tshoperp'
mysql -uroot -ptshoperp -e 'create database jsh_erp;use jsh_erp;source /opt/jsh_erp.sql;'
mysql -uroot -ptshoperp -e "grant all on *.* to  'root'@'%' identified by 'tshoperp';"

```

# 编排redis
```dockerfile
FROM centos:centos7.9.2009
RUN rm -rf /etc/yum.repos.d/*
COPY yum /root/yum
COPY local.repo /etc/yum.repos.d/
RUN yum -y install redis
RUN sed -i "s/127.0.0.1/0.0.0.0/g" /etc/redis.conf && \
    sed -i "s/protected-mode yes/protected-mode no/g" /etc/redis.conf
RUN echo "requirepass 123456" >> /etc/redis.conf
EXPOSE 6379
CMD ["redis-server","/etc/redis.conf"]
```

# 编排nginx
```dockerfile
FROM centos:centos7.9.2009
RUN rm -rf /etc/yum.repos.d/*
COPY yum /root/yum
COPY local.repo /etc/yum.repos.d/
RUN yum -y install nginx
ADD nginx/app.tar.gz /
COPY nginx/nginx.conf /etc/nginx/
EXPOSE 80 443
CMD ["nginx","-g","daemon off;"]

```

# 编排erp
```dockerfile
FROM centos:centos7.9.2009
RUN rm -rf /etc/yum.repos.d/*
COPY yum /root/yum
COPY local.repo /etc/yum.repos.d/
RUN yum -y install java-1.8.0-openjdk-devel java-1.8.0-openjdk
COPY start.sh /opt
RUN chmod 777 /opt/start.sh
RUN mkdir /jshERP-boot/
COPY app.jar /jshERP-boot/
EXPOSE 9999
CMD ["/bin/bash","/opt/start.sh"]

start.sh
#/bin/bash
java -jar /jshERP-boot/app.jar
```

# 编排docker-compose
```yaml
version: "3"
services:
  erp-mysql:
    image: erp-mysql:v1.0
    container_name: erp-mysql
    ports:
    - 3306:3306
    restart: always

  erp-nginx:
    image: erp-nginx:v1.0
    container_name: erp-nginx
    ports:
    - 8888:80
    restart: always

  erp-redis:
    image: erp-redis:v1.0
    container_name: erp-redis
    ports:
    - 6379:6379
    restart: always

  erp-server:
    image: erp-server:v1.0
    container_name: erp-server
    ports:
    - 9999:9999
    restart: always
    environment:
    - DB_PASSWORD=root
    - DB_USERNAME=tshoperp
    - REDIS_HOST=erp-redis
    - REDIS_PASSWORD=123456
    links:
    - erp-redis
```
