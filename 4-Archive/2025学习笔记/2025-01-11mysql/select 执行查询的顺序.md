# SQL SELECT 执行顺序

SQL 查询语句的执行顺序参考图。

![[1736846880770-d50bf917-7131-4efc-adf4-fa6495c4d1e6.jpeg]]

## 执行顺序

| 顺序 | 子句 | 说明 |
| --- | --- | --- |
| 1 | `FROM` | 确定数据来源表 |
| 2 | `ON` | 连接条件 |
| 3 | `JOIN` | 表连接 |
| 4 | `WHERE` | 行级过滤 |
| 5 | `GROUP BY` | 分组 |
| 6 | `HAVING` | 组级过滤 |
| 7 | `SELECT` | 选择列 |
| 8 | `DISTINCT` | 去重 |
| 9 | `ORDER BY` | 排序 |
| 10 | `LIMIT` | 分页 |
