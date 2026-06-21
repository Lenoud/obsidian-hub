# mysqld_exporter

mysqld_exporter 是 Prometheus 官方维护的 MySQL 指标导出器,通过连接 MySQL 收集性能、复制、InnoDB、查询缓存等指标,以 Prometheus 格式在 `:9104` 暴露。

## 部署(Docker Compose)

Grafana Dashboard 推荐导入:
- **ID 7365**:MySQL Overview
- **ID 9625**:MySQL 表级统计

```yaml
# docker-compose.yml
services:
  mysqld-exporter:
    image: prom/mysqld-exporter
    container_name: mysqld-exporter
    restart: always
    ports:
      - 9104:9104
    environment:
      - DATA_SOURCE_NAME=prometheus:000000@(192.168.100.251:3306)/
    command:
      - '--collect.info_schema.processlist'
      - '--collect.info_schema.innodb_metrics'
      - '--collect.info_schema.tablestats'
      - '--collect.info_schema.tables'
      - '--collect.info_schema.userstats'
      - '--collect.engine_innodb_status'
```

## MySQL 端准备

在 MySQL 中创建专用监控账号(只读权限):

```sql
CREATE USER 'prometheus'@'%' IDENTIFIED BY '000000';
GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'prometheus'@'%';
FLUSH PRIVILEGES;
```

## Prometheus 抓取配置

```yaml
scrape_configs:
  - job_name: 'mysql'
    static_configs:
      - targets: ['192.168.100.251:9104']
```

## 验证

```bash
# 看 metrics 是否能拉到
curl http://192.168.100.251:9104/metrics | grep mysql_up
# mysql_up 1     ← 1 表示连接成功
```

## 常用采集器

| 采集器 | 说明 |
| --- | --- |
| `--collect.info_schema.processlist` | 当前进程列表 |
| `--collect.info_schema.innodb_metrics` | InnoDB 指标 |
| `--collect.info_schema.tablestats` | 表统计 |
| `--collect.engine_innodb_status` | InnoDB 引擎状态 |
| `--collect.slave_status` | 主从复制状态(从库用) |
| `--collect.global_status` | `SHOW GLOBAL STATUS` |

## 相关笔记
- [[node_exporter]] — Linux 监控
- [[windows_exporter]] — Windows 监控
- [[promethues系统监控]] — Prometheus 总览
- [[docker部署mysql]] — MySQL Docker 部署
- [[MOC-服务部署]]

