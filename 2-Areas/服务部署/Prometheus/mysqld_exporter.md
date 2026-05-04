# mysqld_exporter

mysqld_exporter相关技术笔记。

SQL 语句使用示例。

导入7362和9625，分别为mysql数据库，和mysql数据表的dashboard-ID号

```shell
cat >> docker-compose.yaml<< EOF
version: '3.0'
services:
  mysql-exporter:
    image: prom/mysqld-exporter
    container_name: mysqld-exporter
    restart: always
    command:
     - '--collect.info_schema.processlist'
     - '--collect.info_schema.innodb_metrics'
     - '--collect.info_schema.tablestats'
     - '--collect.info_schema.tables'
     - '--collect.info_schema.userstats'
     - '--collect.engine_innodb_status'
    environment:
     - DATA_SOURCE_NAME=prometheus:000000@(192.168.100.251:3306)/
    ports:
     - 9104:9104
EOF
```

## 相关笔记

- [[mysql8]]
- [[docker部署mysql]]
- [[MOC-数据库]]
- [[MOC-Docker]]
