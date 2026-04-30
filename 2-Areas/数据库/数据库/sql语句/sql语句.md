# SQL 语句全面参考

涵盖 DDL、DML、DQL、DCL 四大类 SQL 操作的语法速查。

参考文章：https://blog.csdn.net/promsing/article/details/112793260 、 https://blog.csdn.net/ww166955/article/details/121699057

## DDL — 数据定义语言

### 数据库操作

```sql
CREATE DATABASE db1;
CREATE DATABASE IF NOT EXISTS db1;
SHOW DATABASES;
SHOW CREATE DATABASE db1;
ALTER DATABASE db1 CHARACTER SET utf8;
DROP DATABASE db1;
```

### 表操作

```sql
CREATE TABLE student(
  id INT,
  name VARCHAR(32),
  age INT,
  score DOUBLE(4,1),
  birthday DATE,
  insert_time TIMESTAMP
);

DESC 表名;
SHOW CREATE TABLE 表名;
ALTER TABLE 表名 RENAME TO 新的表名;
ALTER TABLE 表名 ADD 列名 数据类型;
ALTER TABLE 表名 DROP 列名;
DROP TABLE 表名;
DROP TABLE IF EXISTS 表名;
```

## DML — 数据操作语言

```sql
-- 插入数据
INSERT INTO 表名(列名1,列名2,...列名n) VALUES(值1,值2,...值n);
INSERT INTO 表名 VALUES(值1,值2,...值n);
INSERT INTO 表名(列名1,列名2) VALUES(值1,值2);

-- 删除数据
DELETE FROM 表名 WHERE 列名 = 值;
DELETE FROM 表名;
TRUNCATE TABLE 表名;

-- 修改数据
UPDATE 表名 SET 列名 = 值;
UPDATE 表名 SET 列名 = 值 WHERE 列名=值;
```

## DQL — 数据查询语言

### 条件查询

```sql
-- 范围查询
SELECT * FROM student WHERE age BETWEEN 20 AND 30;

-- 集合查询
SELECT * FROM student WHERE age IN (22,18,25);

-- 非空判断
SELECT * FROM student WHERE english IS NOT NULL;

-- 模糊查询（_ 单字符，% 多字符）
SELECT * FROM student WHERE NAME LIKE '马%';
SELECT * FROM student WHERE NAME LIKE '_化%';
SELECT * FROM student WHERE NAME LIKE '___';
SELECT * FROM student WHERE NAME LIKE '%德%';

-- 去重
SELECT DISTINCT NAME FROM student;
```

### 排序查询

```sql
SELECT * FROM person ORDER BY math;          -- 升序
SELECT * FROM person ORDER BY math DESC;     -- 降序
```

### 聚合函数

| 函数 | 说明 |
| --- | --- |
| `COUNT()` | 计算个数 |
| `MAX()` | 最大值 |
| `MIN()` | 最小值 |
| `SUM()` | 求和 |
| `AVG()` | 平均值 |

### 分组查询

```sql
SELECT sex, AVG(math) FROM student GROUP BY sex;
SELECT sex, AVG(math), COUNT(id) FROM student GROUP BY sex;
SELECT sex, AVG(math), COUNT(id) FROM student WHERE math > 70 GROUP BY sex;
SELECT sex, AVG(math), COUNT(id) 人数 FROM student WHERE math > 70 GROUP BY sex HAVING 人数 > 2;
```

### 分页查询

```sql
-- 语法：LIMIT 开始的索引, 每页条数
-- 公式：开始的索引 = (当前页码 - 1) * 每页显示条数
SELECT * FROM student LIMIT 0,3;   -- 第1页
SELECT * FROM student LIMIT 3,3;   -- 第2页
SELECT * FROM student LIMIT 6,3;   -- 第3页
```

### 内连接查询

```sql
-- 隐式内连接
SELECT t1.name, t1.gender, t2.name
FROM emp t1, dept t2
WHERE t1.dept_id = t2.id;

-- 显式内连接
SELECT * FROM emp INNER JOIN dept ON emp.dept_id = dept.id;
```

### 外连接查询

```sql
-- 左外连接（左表全部 + 交集）
SELECT t1.*, t2.name FROM emp t1 LEFT JOIN dept t2 ON t1.dept_id = t2.id;

-- 右外连接（右表全部 + 交集）
SELECT * FROM dept t2 RIGHT JOIN emp t1 ON t1.dept_id = t2.id;
```

### 子查询

```sql
-- 单行单列：作为条件
SELECT * FROM emp WHERE salary = (SELECT MAX(salary) FROM emp);

-- 多行单列：配合 IN 使用
SELECT * FROM emp WHERE dept_id IN (SELECT id FROM dept WHERE name = '财务部' OR name = '市场部');

-- 多行多列：作为虚拟表
SELECT * FROM dept t1, (SELECT * FROM emp WHERE join_date > '2011-11-11') t2 WHERE t1.id = t2.dept_id;
```

## DCL — 数据控制语言

### 用户管理

```sql
-- 创建用户
CREATE USER 'user_name'@'host' IDENTIFIED BY 'password';
CREATE USER 'user_name'@'%' IDENTIFIED BY 'password';

-- 删除用户
DROP USER '用户名'@'主机名';

-- 修改密码
SET PASSWORD FOR 'root'@'%' = PASSWORD('123456');
```

### 权限管理

```sql
-- 查询权限
SHOW GRANTS FOR '用户名'@'主机名';

-- 授予权限
GRANT ALL ON *.* TO 'zhangsan'@'localhost';
GRANT SELECT ON *.* TO 'lwx'@localhost IDENTIFIED BY 'lwx';
GRANT ALL ON test.* TO lwx@'192.168.0.%' IDENTIFIED BY '123456';
GRANT SELECT,INSERT,UPDATE,DELETE ON test.* TO lwx@'%' IDENTIFIED BY 'password';

-- 撤销权限
REVOKE UPDATE ON db3.account FROM 'lisi'@'%';
```
