# SQL ORDER BY 排序查询

使用 `ORDER BY` 关键字对查询结果集按一个或多个列进行排序，默认升序，使用 `DESC` 降序。

## 语法

```sql
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2, ... ASC|DESC;
```

## 示例

```sql
-- 默认升序
SELECT * FROM Websites ORDER BY alexa;

-- 降序
SELECT * FROM Websites ORDER BY alexa DESC;

-- 多列排序
SELECT * FROM Websites ORDER BY country, alexa;
```

![[1705285486667-56f6a84d-f08a-4fea-8582-5365cee86b3a.png]]

## 说明

| 关键字 | 说明 |
| --- | --- |
| `ASC` | 升序排序（默认） |
| `DESC` | 降序排序 |
| 多列排序 | 先按第一列排，值相同时按第二列排 |
