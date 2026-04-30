# Redis 常用命令

Redis 数据库操作的常用命令速查表。

## 基础操作

| 命令 | 描述 |
| --- | --- |
| `SET key value` | 设置键值对 |
| `GET key` | 获取键的值 |
| `DEL key` | 删除键 |
| `KEYS *` | 查看所有键 |
| `EXISTS key` | 判断键是否存在 |
| `EXPIRE key seconds` | 设置键的过期时间 |
| `TTL key` | 查看键的剩余过期时间 |
| `FLUSHALL` | 清空所有数据库 |
