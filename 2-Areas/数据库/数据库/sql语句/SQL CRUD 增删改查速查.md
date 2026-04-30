# SQL CRUD 增删改查速查表

数据库增删改查（CRUD）操作的标准语法和常用模式速查。

## INSERT — 插入数据

```sql
-- 插入单条
INSERT INTO users (name, age, email) VALUES ('张三', 25, 'zhangsan@example.com');

-- 插入多条
INSERT INTO users (name, age) VALUES ('李四', 30), ('王五', 28);

-- 从另一张表插入
INSERT INTO users_backup (name, age) SELECT name, age FROM users WHERE age > 20;

-- 冲突时更新（MySQL: ON DUPLICATE KEY UPDATE）
INSERT INTO users (id, name, age) VALUES (1, '张三', 26)
ON DUPLICATE KEY UPDATE name = VALUES(name), age = VALUES(age);

-- 冲突时忽略
INSERT IGNORE INTO users (id, name) VALUES (1, '张三');
```

## SELECT — 查询数据

```sql
-- 基础查询
SELECT * FROM users;
SELECT name, age FROM users;

-- 条件查询
SELECT * FROM users WHERE age > 20 AND name LIKE '%张%';
SELECT * FROM users WHERE age BETWEEN 20 AND 30;
SELECT * FROM users WHERE age IN (20, 25, 30);

-- 排序与分页
SELECT * FROM users ORDER BY age DESC LIMIT 10 OFFSET 0;

-- 聚合查询
SELECT COUNT(*) AS total, AVG(age) AS avg_age FROM users;
SELECT department, COUNT(*) AS count FROM users GROUP BY department HAVING count > 5;

-- 多表 JOIN
SELECT u.name, o.order_no FROM users u
LEFT JOIN orders o ON u.id = o.user_id;

-- 子查询
SELECT * FROM users WHERE id IN (SELECT user_id FROM orders WHERE amount > 1000);
```

## UPDATE — 更新数据

```sql
-- 更新单个字段
UPDATE users SET name = '新名字' WHERE id = 1;

-- 更新多个字段
UPDATE users SET name = '新名字', age = 26, updated_at = NOW() WHERE id = 1;

-- 条件批量更新
UPDATE users SET status = 'inactive' WHERE last_login < DATE_SUB(NOW(), INTERVAL 90 DAY);

-- 关联更新
UPDATE users u JOIN orders o ON u.id = o.user_id
SET u.total_orders = o.order_count;

-- 使用 CASE 批量条件更新
UPDATE products SET price = CASE
    WHEN category = 'A' THEN price * 1.1
    WHEN category = 'B' THEN price * 1.05
    ELSE price
END;
```

## DELETE — 删除数据

```sql
-- 删除单条
DELETE FROM users WHERE id = 1;

-- 条件删除
DELETE FROM users WHERE last_login < '2024-01-01';

-- 清空表（重置自增ID，不可回滚）
TRUNCATE TABLE users;

-- 关联删除
DELETE u FROM users u LEFT JOIN profiles p ON u.id = p.user_id WHERE p.id IS NULL;
```

## 说明

| 操作 | SQL 关键字 | 返回值 |
| --- | --- | --- |
| Create | `INSERT INTO` | 影响行数 |
| Read | `SELECT` | 结果集 |
| Update | `UPDATE SET` | 影响行数 |
| Delete | `DELETE FROM` | 影响行数 |
