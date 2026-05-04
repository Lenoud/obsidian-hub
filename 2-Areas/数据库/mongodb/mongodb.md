# MongoDB 常用命令

MongoDB 数据库操作的常用命令速查表。

| 命令 | 描述 |
| --- | --- |
| `show dbs` | 显示所有数据库 |
| `use <database>` | 切换到指定数据库 |
| `show collections` | 显示当前数据库所有集合 |
| `db.<col>.find()` | 查询集合中所有文档 |
| `db.<col>.find(<query>)` | 条件查询 |
| `db.<col>.insert(<doc>)` | 插入文档 |
| `db.<col>.update(<query>,<update>)` | 更新文档 |
| `db.<col>.remove(<query>)` | 删除文档 |
| `show users` | 显示所有用户 |
| `db.createUser(<user>)` | 创建用户 |
| `db.dropUser(<name>)` | 删除用户 |
| `db.auth(<user>,<pwd>)` | 身份验证 |

## 备份与恢复

| 命令 | 描述 |
| --- | --- |
| `mongodump --db <db> --out <dir>` | 备份数据库 |
| `mongorestore --db <db> <dir>` | 恢复数据库 |
| `mongoexport -d <db> -c <col> -o <file>` | 导出集合 |
| `mongoimport -d <db> -c <col> --file <file>` | 导入集合 |
| `mongostat` | 显示统计信息 |
| `mongotop` | 显示操作性能统计 |
