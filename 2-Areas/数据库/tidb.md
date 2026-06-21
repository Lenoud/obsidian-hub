# TiDB 分布式数据库

TiDB 是由 PingCAP 开源的一款分布式 HTAP（Hybrid Transactional and Analytical Processing）数据库，兼容 MySQL 协议，支持水平扩缩容、强一致事务和高可用，适用于大数据量、高并发、混合负载场景。

## 核心架构

TiDB 采用**计算与存储分离**的分布式架构，由多个独立组件协同工作：

| 组件 | 作用 | 说明 |
| --- | --- | --- |
| **TiDB Server** | SQL 层 | 接收客户端连接，解析 SQL、生成执行计划，无状态，可水平扩展 |
| **TiKV** | 行存引擎 | 分布式 KV 事务存储，使用 Raft 协议保证强一致性 |
| **TiFlash** | 列存引擎 | 实时分析副本，与 TiKV 通过 Raft Learner 同步，支撑 OLAP |
| **PD（Placement Driver）** | 调度元信息 | 管理集群元数据、Region 调度、时间戳分配（TSO） |

架构关系示意：

```text
            ┌─────────────┐
   Client ─▶│ TiDB Server │── 负责协议兼容、SQL 解析、执行计划
            └──────┬──────┘
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
   ┌─────────┐ ┌─────────┐ ┌──────────┐
   │  TiKV   │ │  TiKV   │ │ TiFlash  │
   │ 行存 OLTP│ │ 行存    │ │ 列存 OLAP│
   └────┬────┘ └────┬────┘ └─────┬────┘
        │           │            │
        └─────┬─────┘            │
              ▼                  ▼
         ┌──────────────────────────┐
         │ PD（Placement Driver）    │
         │ 元信息、调度、TSO         │
         └──────────────────────────┘
```

## 主要特性

| 特性 | 说明 |
| --- | --- |
| **MySQL 兼容** | 大多数情况下可直接替换 MySQL，无需改业务代码 |
| **水平扩缩容** | 按节点扩容存储与算力，业务无感 |
| **ACID 事务** | 基于 Percolator 模型，支持跨行/跨表强一致事务 |
| **HTAP** | 一份数据同时服务 OLTP 与 OLAP，无需 ETL |
| **高可用** | Raft 多副本，自动故障转移 |
| **云原生** | 原生支持 Kubernetes 部署（TiDB Operator） |

## 与 MySQL 的兼容性

TiDB 兼容 MySQL 5.7 协议及大部分语法，业务侧常用 ORM（MyBatis、GORM、Django ORM 等）基本可无缝接入，但仍有差异需要注意：

| 维度 | 差异说明 |
| --- | --- |
| 自增 ID | TiDB 自增列**不保证连续**，仅在单节点内递增；跨节点可能乱序 |
| 存储引擎 | 不支持 InnoDB/MyISAM 等引擎名，建表时忽略 ENGINE 子句 |
| 外键 | 不强制外键约束（语法兼容但不生效） |
| 触发器/存储过程 | 部分支持，复杂场景有差异 |
| 字符集 | 默认 utf8mb4，推荐 `utf8mb4_bin` 排序规则 |
| 事务隔离 | 默认 **Snapshot Isolation**（接近 RR），不支持脏读 |
| DDL | 在线 DDL，但 ALTER 大表需注意性能 |
| AUTO_RANDOM | TiDB 独有特性，替代 AUTO_INCREMENT 解决写热点 |

> 实践建议：从 MySQL 迁移前使用 `tidb-lightning` 导入全量数据，再用 DM（Data Migration）同步增量。

## Docker Compose 最小部署示例

以下示例使用 PingCAP 官方镜像，部署 1 个 PD + 1 个 TiKV + 1 个 TiDB，仅供学习测试，**生产环境请使用 TiUP 或 TiDB Operator**。

`docker-compose.yml`：

```yaml
version: "3.7"
services:
  pd:
    image: pingcap/pd:v7.5.0
    container_name: pd
    command:
      - --name=pd
      - --client-urls=http://0.0.0.0:2379
      - --peer-urls=http://0.0.0.0:2380
      - --initial-cluster=pd=http://pd:2380
    ports:
      - "2379:2379"
      - "2380:2380"
    restart: unless-stopped

  tikv:
    image: pingcap/tikv:v7.5.0
    container_name: tikv
    depends_on: [pd]
    command:
      - --addr=0.0.0.0:20160
      - --advertise-addr=tikv:20160
      - --pd=pd:2379
    restart: unless-stopped

  tidb:
    image: pingcap/tidb:v7.5.0
    container_name: tidb
    depends_on: [tikv]
    command:
      - --store=tikv
      - --path=pd:2379
    ports:
      - "4000:4000"   # MySQL 协议端口
      - "10080:10080" # status 端口
    restart: unless-stopped
```

启动与连接：

```bash
docker compose up -d
mysql -h 127.0.0.1 -P 4000 -u root
```

验证版本：

```sql
SELECT version();
SELECT tidb_version()\G
```

输出示例：

```text
+----------------------+
| version()            |
+----------------------+
| 5.7.45-TiDB-v7.5.0   |
+----------------------+
```

## 相关笔记

- [[MOC-数据库]]
- [[Mariadb]]
- [[Mysql数据库导入导出]]
- [[sql语句]]
