# PostgreSQL 数据库

PostgreSQL（简称 PG）是一款开源的对象关系型数据库，以标准合规、强扩展性、数据类型丰富、稳定性高著称，被称为"最先进的开源数据库"。本文整理 psql 常用命令、数据类型、与 MySQL 的关键差异以及角色权限管理要点。

## psql 常用命令

psql 是 PostgreSQL 的命令行客户端，元命令以 `\` 开头，不加分号结尾。

| 命令 | 作用 |
| --- | --- |
| `\l` | 列出所有数据库 |
| `\l+` | 列出数据库详细信息 |
| `\c dbname` 或 `\connect dbname` | 切换到数据库 dbname |
| `\dt` | 列出当前 schema 的所有表 |
| `\dt+` | 列出表（含大小、描述） |
| `\d tablename` | 查看表结构 |
| `\d+ tablename` | 查看表结构详细信息（含注释、存储） |
| `\du` | 列出所有角色（用户） |
| `\dx` | 列出已安装的扩展 |
| `\dn` | 列出所有 schema |
| `\df` | 列出函数 |
| `\dv` | 列出视图 |
| `\x` | 切换扩展显示（行转列，便于阅读宽表） |
| `\timing` | 开启/关闭 SQL 执行计时 |
| `\q` | 退出 psql |
| `\?` | 查看所有元命令帮助 |
| `\h CREATE TABLE` | 查看 SQL 命令帮助 |
| `\i script.sql` | 执行外部 SQL 脚本 |

登录数据库：

```bash
sudo -u postgres psql          # 本机默认 postgres 用户
psql -h 10.0.0.1 -U dbuser -d dbname -p 5432
```

## 常用数据类型

| 类别 | 类型 | 说明 |
| --- | --- | --- |
| 数值 | `smallint` / `integer` / `bigint` | 2/4/8 字节整数 |
| 数值 | `serial` / `bigserial` | 自增整数（底层是 SEQUENCE） |
| 数值 | `numeric(p, s)` / `decimal(p, s)` | 任意精度小数，财务场景推荐 |
| 数值 | `real` / `double precision` | 4/8 字节浮点 |
| 字符 | `char(n)` | 定长，不足补空格 |
| 字符 | `varchar(n)` | 变长，有上限 |
| 字符 | `text` | 变长，无上限，推荐 |
| 时间 | `timestamp` / `timestamptz` | 时间戳，后者带时区 |
| 时间 | `date` / `time` | 日期 / 时间 |
| 时间 | `interval` | 时间间隔，可用于日期运算 |
| 布尔 | `boolean` | `true` / `false` / `NULL` |
| JSON | `json` / `jsonb` | 后者为二进制存储，可索引、性能更好 |
| 二进制 | `bytea` | 二进制大对象 |
| UUID | `uuid` | 需先 `CREATE EXTENSION pgcrypto` 或 `uuid-ossp` |
| 数组 | `int[]` / `text[]` | 任意基础类型均可构成数组 |
| 自增 | `GENERATED ALWAYS AS IDENTITY` | SQL 标准写法，PG 10+ 推荐 |

建表示例：

```sql
CREATE TABLE users (
    id          bigserial PRIMARY KEY,
    name        text       NOT NULL,
    email       varchar(255) UNIQUE,
    age         smallint   CHECK (age >= 0),
    meta        jsonb      DEFAULT '{}'::jsonb,
    tags        text[]     DEFAULT '{}',
    created_at  timestamptz DEFAULT now()
);

-- 数组查询
SELECT * FROM users WHERE 'vip' = ANY(tags);

-- JSONB 查询
SELECT * FROM users WHERE meta @> '{"role":"admin"}';
```

## 与 MySQL 的关键差异

| 维度 | PostgreSQL | MySQL |
| --- | --- | --- |
| Schema 概念 | Database 下有多个 **Schema**（默认 public），表归属 Schema | 数据库等同 Schema，无独立层级 |
| 大小写敏感 | **默认敏感**，未加引号的标识符会被转为小写 | 默认不敏感（取决于 `lower_case_table_names`） |
| 字符串引号 | 双引号 `"col"` 表示**标识符**（列名/表名），单引号 `'str'` 表示字符串 | 双引号也可作标识符，单引号是字符串 |
| 自增 | `serial` 或 `GENERATED ... AS IDENTITY` | `AUTO_INCREMENT` |
| 序列对象 | 显式 `CREATE SEQUENCE`，可手动调用 `nextval()` | 无独立 SEQUENCE 对象 |
| INSERT 返回值 | 支持 `RETURNING *` 直接返回插入/更新后的行 | 仅 MySQL 8.0.21+ 等有限支持 |
| 事务隔离 | 默认 Read Committed，支持 Serializable（SSI） | 默认 Repeatable Read |
| 存储过程 | 函数（`CREATE FUNCTION`）+ 存储过程（`CREATE PROCEDURE`，PG11+） | 一直支持 PROCEDURE |
| 空值排序 | `NULL` 默认排最后，`ORDER BY col NULLS FIRST` 可控 | `NULL` 默认最小 |
| 分页 | `LIMIT n OFFSET m` | 同样支持，也有 `LIMIT` |

`RETURNING` 用法（PG 强大特性）：

```sql
INSERT INTO users (name) VALUES ('alice') RETURNING id, created_at;

UPDATE users SET age = 18 WHERE name = 'alice' RETURNING *;

DELETE FROM users WHERE age < 18 RETURNING id, name;
```

## 角色 / 权限管理

PostgreSQL 采用 **ROLE** 模型，USER 与 ROLE 等价（USER 默认带 LOGIN 属性）。

```sql
-- 创建角色并赋权
CREATE ROLE app_user WITH LOGIN PASSWORD 'StrongPwd123';
CREATE DATABASE appdb OWNER app_user;

-- 授权
GRANT CONNECT ON DATABASE appdb TO app_user;
GRANT USAGE ON SCHEMA public TO app_user;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_user;
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO app_user;

-- 设置默认权限（之后新建的表自动授权）
ALTER DEFAULT PRIVILEGES IN SCHEMA public
    GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO app_user;
```

权限层级一览（自顶向下）：

```text
DATABASE  →  SCHEMA  →  TABLE / SEQUENCE / FUNCTION
                 └─ GRANT ... ON ALL TABLES IN SCHEMA ...
```

常用权限查询：

```sql
\du                                -- 角色列表
\dp                                -- 表权限
SELECT * FROM information_schema.role_table_grants WHERE grantee = 'app_user';
```

## 相关笔记

- [[docker部署postgresql]]
- [[MOC-数据库]]
- [[sql语句]]
- [[Mariadb]]
