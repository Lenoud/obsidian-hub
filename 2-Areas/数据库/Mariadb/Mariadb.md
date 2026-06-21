# MariaDB 数据库

MariaDB 是由 MySQL 原作者 Michael "Monty" Widenius 发起的分支,完全兼容 MySQL 协议和 API,定位为 MySQL 的"自由版本"。2010 年 Oracle 收购 Sun(及其 MySQL)后,社区担心 MySQL 闭源,MariaDB 成为保障开源生态的核心项目。

## 与 MySQL 的关系

| 维度 | MariaDB | MySQL |
| --- | --- | --- |
| 维护方 | MariaDB Foundation(社区) | Oracle |
| 协议兼容 | 与 MySQL 完全兼容 | — |
| 存储引擎 | XtraDB、Aria、ColumnStore、RocksDB 等 | InnoDB、MyISAM |
| 驱动 | MySQL Connector、MariaDB Connector | — |
| 命令行 | `mysql`(同名) | `mysql` |
| 配置文件 | `/etc/mysql/mariadb.conf.d/` | `/etc/mysql/conf.d/` |
| 默认字符集(MariaDB 10.5+/MySQL 8) | utf8mb4 | utf8mb4 |

大多数应用代码可以无缝切换。

## 版本与 MySQL 对应

| MariaDB 版本 | 对应 MySQL 版本 | 说明 |
| --- | --- | --- |
| **11.x**(推荐) | MySQL 8.0+(部分超越) | 最新 LTS,完全兼容 MySQL 8 + 优化 |
| 10.10-10.11 | MySQL 8.0 | LTS 版本 |
| 10.6 | MySQL 5.7 + 部分 8.0 | 长期支持 |
| 10.5 | MySQL 8.0(部分) | 引入 `RETURNING`、S3 引擎 |
| 10.4 | MySQL 5.7 | 优化密码认证 |
| 10.3 | MySQL 5.7 + 窗口函数 | 系统版本化表 |
| 10.2 | MySQL 5.6 | CTE、窗口函数开始 |
| 10.0 / 10.1 | MySQL 5.5 / 5.6 | 老版本,不推荐 |

![MariaDB 与 MySQL 版本对照](../attachments/1721313951750-5e0be47b-4358-44d6-8b72-7d3f036baa2c.png)

## 独有特性(相对 MySQL)

| 特性 | 说明 |
| --- | --- |
| **Galera Cluster** | 多主同步集群(MySQL 用 Group Replication) |
| **ColumnStore** | 列式存储 OLAP 引擎 |
| **Aria** | 崩溃安全的 MyISAM 替代 |
| **系统版本化表** | 历史数据自动归档(SQL:2011 标准) |
| **RETURNING 子句** | `DELETE ... RETURNING`(类似 PostgreSQL) |
| **SEQUENCE 引擎** | 原生序列(类似 PostgreSQL) |
| **EXECUTE IMMEDIATE** | 动态 SQL |

## 安装

### CentOS / RHEL / Rocky

```bash
# 配置 MariaDB 官方源(以 10.11 为例)
cat > /etc/yum.repos.d/MariaDB.repo <<EOF
[mariadb]
name = MariaDB
baseurl = https://mirrors.tuna.tsinghua.edu.cn/mariadb/yum/10.11/centos8-amd64
gpgkey = https://mirrors.tuna.tsinghua.edu.cn/mariadb/yum/RPM-GPG-KEY-MariaDB
gpgcheck = 1
EOF

dnf install -y MariaDB-server MariaDB-client
systemctl enable --now mariadb
mysql_secure_installation          # 初始化(设置 root 密码等)
```

### Docker

见 [[docker部署mariadb]]。

## 常用命令

```bash
# 登录
mysql -uroot -p

# 版本
SELECT VERSION();
# 10.11.6-MariaDB-1:10.11.6+maria~deb10

# 查看存储引擎
SHOW ENGINES;

# Galera 集群状态
SHOW STATUS LIKE 'wsrep_cluster_size';
```

## 相关笔记
- [[docker部署mariadb]] — Docker 部署
- [[mariadb数据库主从配置]] — 异步主从复制
- [[Mariadb]] — 本文(MOC-数据库 入口)
- [[MOC-数据库]]
