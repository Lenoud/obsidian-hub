# MySQL 8 数据库

新一代关系型数据库，相比 5.7 版本提供了更好的性能、安全性和 JSON 支持。

## 部署

```yaml
version: "3.8"
services:
  mysql:
    hostname: mysql8
    container_name: mysql8
    image: mysql:8.0.36
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: "000000"
    volumes:
      - ./my.cnf:/etc/my.cnf
      - /etc/localtime:/etc/localtime:ro
      - ./mysql-data:/var/lib/mysql
    network_mode: db_bridge
    ports:
      - 3307:3306
```

## my.cnf 配置

```ini
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M

# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
# default-authentication-plugin=mysql_native_password
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

max_allowed_packet=1073741824

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
```

## 说明

- 访问端口：`3307`（映射到容器内 `3306`）
- `MYSQL_ROOT_PASSWORD`：root 用户密码
- 数据持久化：`./mysql-data` 映射到 `/var/lib/mysql`
- 自定义配置：`./my.cnf` 映射到 `/etc/my.cnf`
- `max_allowed_packet=1073741824`：最大允许的数据包大小为 1GB
- `network_mode: db_bridge`：使用自定义网络

## 相关笔记
- [[docker部署mysql]]
- [[mysql57]]
- [[MOC-Docker]]
