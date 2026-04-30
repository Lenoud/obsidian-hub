# PyMySQL 连接 MySQL 数据库

使用 Python 的 `pymysql` 库连接 MySQL 数据库并执行查询。

## 基本用法

```python
import pymysql

db = pymysql.connect(host='192.168.1.22', user='root', password='000000', database='zb360')
cursor = db.cursor()

# 执行 SQL 查询
cursor.execute("SELECT VERSION()")

# 获取单条数据
data = cursor.fetchone()
print("Database version : %s " % data)

# 关闭数据库连接
db.close()
```

## 说明

| 方法 | 说明 |
| --- | --- |
| `pymysql.connect()` | 创建数据库连接 |
| `cursor()` | 创建游标对象 |
| `execute()` | 执行 SQL 语句 |
| `fetchone()` | 获取一条查询结果 |
| `fetchall()` | 获取所有查询结果 |
| `db.close()` | 关闭数据库连接 |
