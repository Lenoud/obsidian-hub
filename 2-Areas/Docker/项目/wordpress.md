# WordPress 博客系统

开源的内容管理系统（CMS），广泛用于博客、企业网站和电商平台，搭配 MySQL 数据库使用。

## 部署

```yaml
version: '3.8'

services:

  wordpress:
    image: wordpress:6.6.2
    restart: always
    hostname: wordpress
    container_name: wordpress
    ports:
      - 80:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DEBUG: 0
    volumes:
      - ./wordpress_data:/var/www/html
      - ./conf/uploads.ini:/usr/local/etc/php/conf.d/uploads.ini

  db:
    hostname: mysql57
    container_name: mysql57
    image: mysql:5.7
    restart: always
    user: root
    environment:
      MYSQL_ROOT_PASSWORD: "000000"
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    ports:
      - 3306:3306
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./my.cnf:/etc/my.cnf
      - ./data:/var/lib/mysql
      - ./root:/root
```

## 说明

- WordPress 访问地址：`http://<IP>:80`
- 数据库端口：`3306`
- `WORDPRESS_DB_HOST`：WordPress 连接的数据库主机名（`db` 为 compose 内部服务名）
- `WORDPRESS_DB_USER` / `WORDPRESS_DB_PASSWORD`：WordPress 数据库用户和密码
- `WORDPRESS_DB_NAME`：WordPress 使用的数据库名
- `WORDPRESS_DEBUG`：调试模式（0 为关闭）
- `MYSQL_ROOT_PASSWORD`：MySQL root 用户密码
- WordPress 数据持久化：`./wordpress_data` 映射到 `/var/www/html`
- MySQL 数据持久化：`./data` 映射到 `/var/lib/mysql`
- PHP 上传配置：`./conf/uploads.ini` 映射到 `/usr/local/etc/php/conf.d/uploads.ini`
- MySQL 自定义配置：`./my.cnf` 映射到 `/etc/my.cnf`
