# MySQL 5.6 忘记 root 密码重置方法

通过跳过授权表启动 MySQL 服务来重置 root 用户密码。

## 操作步骤

### 1. 停止 MySQL 服务

```bash
systemctl stop mysqld
systemctl status mysqld
```

### 2. 跳过授权表启动

编辑 `/etc/my.cnf`，在 `[mysqld]` 段添加：

```ini
skip_grant_tables=1
```

### 3. 重启 MySQL

```bash
systemctl start mysqld
```

### 4. 免密登录并重置密码

```bash
mysql
```

```sql
-- MySQL 5.6
use mysql;
UPDATE user SET Password = password('新密码') WHERE User = 'root';
FLUSH PRIVILEGES;
exit
```

> MySQL 8.0 使用：`update user set authentication_string='' where user='root';`

### 5. 恢复正常启动

删除 `my.cnf` 中的 `skip_grant_tables=1`，然后重启服务：

```bash
systemctl restart mysqld
```

### 6. 验证新密码

```bash
mysql -uroot -p新密码
```

参考文章：https://cloud.tencent.com/developer/article/1855931
