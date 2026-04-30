# MySQL 8 给予 root 用户远程登录权限

在 MySQL 8.x 中通过修改 `mysql.user` 表授予 root 用户远程登录权限。

## 操作步骤

```sql
mysql> use mysql
Database changed

mysql> update user set host='%' where user ='root';
Query OK, 1 row affected (0.02 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> update user set plugin='mysql_native_password' where user ='root';
Query OK, 0 rows affected (0.01 sec)
Rows matched: 1  Changed: 0  Warnings: 0

mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)
```

## 说明

| 步骤 | SQL 语句 | 作用 |
| --- | --- | --- |
| 1 | `update user set host='%'` | 允许 root 从任意主机连接 |
| 2 | `update user set plugin='mysql_native_password'` | 使用原生密码认证插件 |
| 3 | `flush privileges` | 刷新权限使修改生效 |
