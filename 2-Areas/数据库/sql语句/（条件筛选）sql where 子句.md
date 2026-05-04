# SQL WHERE 条件筛选

使用 `WHERE` 子句提取满足指定条件的记录。

## 语法

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

## 示例

```sql
SELECT * FROM Websites WHERE country='CN';
```

![[1705284932665-89e629ae-b06f-4609-ac3c-80106e10aa3e.png]]

## 说明

| 运算符 | 描述 |
| --- | --- |
| `=` | 等于 |
| `<>` / `!=` | 不等于 |
| `>` / `<` | 大于 / 小于 |
| `>=` / `<=` | 大于等于 / 小于等于 |
| `BETWEEN` | 在某个范围内 |
| `LIKE` | 模糊匹配 |
| `IN` | 指定多个可能值 |
