# SQL Server 常用查询语句

SQL Server 中查询数据库表名和行数的常用 SQL 语句。

## 查询所有表名

```sql
-- 方法一
SELECT name FROM sysobjects WHERE xtype='u'

-- 方法二
SELECT * FROM sys.tables
```

## 查询所有表名及行数

```sql
SELECT a.name, b.rows
FROM sysobjects AS a
INNER JOIN sysindexes AS b ON a.id = b.id
WHERE (a.type = 'u') AND (b.indid IN (0, 1))
ORDER BY a.name, b.rows DESC
```

## 说明

| 系统表/视图 | 说明 |
| --- | --- |
| `sysobjects` | 系统对象表，`xtype='u'` 筛选用户表 |
| `sys.tables` | 系统视图，直接列出所有用户表 |
| `sysindexes` | 系统索引表，`indid IN (0,1)` 获取表行数 |
