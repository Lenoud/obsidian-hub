# MySQL 5.7 数据库

广泛使用的关系型数据库管理系统，适用于 Web 应用和企业级数据存储场景。

## 部署

```yaml
version: "3.8"
services:
  mysql:
    hostname: mysql57
    container_name: mysql57
    image: mysql:5.7
    restart: always
    user: root
    environment:
      MYSQL_ROOT_PASSWORD: "000000"
    ports:
      - 3306:3306
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./my.cnf:/etc/my.cnf
      - ./data:/var/lib/mysql
      - ./root:/root
    networks:
      db_bridge:
        ipv4_address: 172.20.0.254
networks:
  db_bridge:
    name: db_bridge
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16
```

## my.cnf 配置

```ini
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html

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
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql
max_allowed_packet=1073741824

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

#log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/
```

## 说明

- 访问端口：`3306`
- `MYSQL_ROOT_PASSWORD`：root 用户密码
- 固定 IP：`172.20.0.254`（子网 `172.20.0.0/16`）
- 数据持久化：`./data` 映射到 `/var/lib/mysql`
- 自定义配置：`./my.cnf` 映射到 `/etc/my.cnf`
- `max_allowed_packet=1073741824`：最大允许的数据包大小为 1GB
