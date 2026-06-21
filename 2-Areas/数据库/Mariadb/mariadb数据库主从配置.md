# MariaDB 数据库主从配置

MariaDB 主从复制（Master-Slave Replication）通过 binlog 将主库的写操作异步同步到一个或多个从库，用于读写分离、数据备份与读扩展。本文整理基于传统位点（file+position）的主从配置流程，并补充 GTID 模式与常见问题。

参考文档：https://www.yuque.com/u33971054/atri/rr5g1y38uzwuvk4h

## 复制原理简述

MariaDB 的复制基于 **binlog 事件流**，涉及三个线程：

```text
┌──────────────┐      ① binlog        ┌──────────────┐
│   Master     │ ───────────────────▶│    Slave     │
│              │  ③ 返回 binlog event │              │
│  Binlog Dump │                      │  IO Thread   │
│   Thread     │                      │      ↓       │
└──────────────┘                      │  Relay Log   │
                                      │      ↓       │
                                      │ SQL Thread   │
                                      │  (回放 SQL)  │
                                      └──────────────┘
```

| 步骤 | 说明 |
| --- | --- |
| ① 主库写 binlog | 主库执行事务后写入二进制日志（binlog） |
| ② IO Thread 拉取 | 从库 IO 线程连接主库，请求从指定位点开始的事件 |
| ③ 写 relay log | 拉到的事件写入从库的中继日志（relay log） |
| ④ SQL Thread 回放 | 从库 SQL 线程读取 relay log 并重放，使数据一致 |

复制链路是**异步**的：主库提交后不等从库确认，因此存在复制延迟。

## 主库配置

### 1. 修改 my.cnf

```ini
[mysqld]
server-id           = 1                # 集群内唯一，建议用 IP 末段
log-bin             = mysql-bin        # 开启 binlog，指定前缀
binlog-format       = ROW              # 推荐行级，最安全
expire_logs_days    = 7                # binlog 保留天数
max_binlog_size     = 256M
binlog_row_image    = MINIMAL          # 减小 binlog 体积
gtid_strict_mode    = ON               # GTID 严格模式（可选）
```

修改后重启 MariaDB：

```bash
systemctl restart mariadb
```

确认 binlog 已开启：

```sql
SHOW VARIABLES LIKE 'log_bin';        -- ON
SHOW VARIABLES LIKE 'server_id';      -- 1
SHOW MASTER STATUS;
```

### 2. 创建复制账号

```sql
CREATE USER 'repl'@'192.168.1.%' IDENTIFIED BY 'ReplPwd@2024';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'192.168.1.%';
FLUSH PRIVILEGES;
```

> 建议复制账号只授 `REPLICATION SLAVE` 最小权限，并限制来源网段。

### 3. 查看主库状态（记录位点）

```sql
FLUSH TABLES WITH READ LOCK;     -- 加锁保证一致性（业务可中断时）
SHOW MASTER STATUS\G
```

输出示例：

```text
File: mysql-bin.000003
Position: 1029
Binlog_Do_DB:
Binlog_Ignore_DB:
```

记录 `File` 与 `Position`，从库建立复制关系时使用。锁仅在导出一致性快照时需要，导完即可 `UNLOCK TABLES;`。

### 4. 导出数据快照到从库

```bash
# 主库导出（使用 --master-data=2 自动记录位点，无需手动锁）
mysqldump --master-data=2 --single-transaction --routines --triggers \
    --all-databases -u root -p > all.sql

# 传输到从库并导入
scp all.sql slave-host:/tmp/
mysql -u root -p < /tmp/all.sql
```

## 从库配置

### 1. 修改 my.cnf

```ini
[mysqld]
server-id           = 2                # 必须与主库不同
relay-log           = relay-bin        # 中继日志
read_only           = ON               # 从库只读
log_bin             = mysql-bin        # 若要做级联复制可开启
replicate_do_db     = appdb            # 可选：只复制指定库
```

重启生效：

```bash
systemctl restart mariadb
```

### 2. 建立复制关系

```sql
CHANGE MASTER TO
    MASTER_HOST     = '192.168.1.10',
    MASTER_PORT     = 3306,
    MASTER_USER     = 'repl',
    MASTER_PASSWORD = 'ReplPwd@2024',
    MASTER_LOG_FILE = 'mysql-bin.000003',
    MASTER_LOG_POS  = 1029;
```

### 3. 启动复制并检查

```sql
START SLAVE;
SHOW SLAVE STATUS\G
```

重点检查以下两行必须为 `Yes`：

```text
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
```

## GTID 模式（推荐）

GTID（Global Transaction ID）用 `server_uuid:sequence` 唯一标识每个事务，无需手动记录位点，故障切换更简单。MariaDB 10.0+ 支持，建议生产环境使用。

主库 my.cnf 追加：

```ini
[mysqld]
gtid_strict_mode    = ON
log_bin             = mysql-bin
log_slave_updates   = ON
```

从库建立复制关系（替代上面的 `CHANGE MASTER TO`）：

```sql
CHANGE MASTER TO
    MASTER_HOST     = '192.168.1.10',
    MASTER_PORT     = 3306,
    MASTER_USER     = 'repl',
    MASTER_PASSWORD = 'ReplPwd@2024',
    MASTER_USE_GTID = current_pos;

START SLAVE;
```

GTID 相关查询：

```sql
SHOW GLOBAL VARIABLES LIKE '%gtid%';
SELECT @@gtid_current_pos;
SELECT @@gtid_slave_pos;
```

## 常见问题

### 1. 复制错误跳过（位点模式）

从库执行某条 SQL 失败（如表已存在），可跳过该事务：

```sql
STOP SLAVE;
SET GLOBAL sql_slave_skip_counter = 1;   -- 跳过 1 个事务
START SLAVE;
```

> GTID 模式下不能使用 `sql_slave_skip_counter`，需要手动注入空事务：

```sql
SET gtid_slave_pos = "<uuid>:<seq>";
START SLAVE;
```

### 2. server_id 冲突

主从 `server-id` 相同时会报错：

```text
Fatal error: The slave I/O thread stops because master and slave have equal MySQL server UUIDs
```

解决：修改 my.cnf 中 `server-id` 使其唯一；若是克隆的虚拟机，还需删除 `auto.cnf`（含 `server-uuid`）后重启：

```bash
rm -f /var/lib/mysql/auto.cnf
systemctl restart mariadb
```

### 3. 复制延迟过大

```sql
SHOW SLAVE STATUS\G
-- Seconds_Behind_Master 字段
```

排查方向：
- 从库硬件性能不足（CPU/IO 瓶颈）
- 大事务导致 SQL 线程长时间执行
- 单线程回放：开启**多源复制**或 **并行复制**（`slave_parallel_workers`）
- 网络带宽限制

### 4. 主从不一致

```sql
-- 使用 pt-table-checksum / pt-table-sync 工具校验与修复
pt-table-checksum --host=master -u root -p --databases=appdb
pt-table-sync --execute --sync-to-master h=slave,D=appdb,t=t1
```

## 相关笔记

- [[Mariadb]]
- [[docker部署mariadb]]
- [[MOC-数据库]]
