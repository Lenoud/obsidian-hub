# MySQL 5 给予 root 用户远程登录权限

在 MySQL 5.x 版本中,通过 `GRANT ... IDENTIFIED BY` 一步完成授权和密码设置。

## MySQL 5.x(经典语法)

```sql
-- 方式一:命令行执行(-e)
mysql -uroot -p000000 -e "grant all privileges on *.* to root@'%' identified by '000000';"

-- 方式二:进入 MySQL 后执行
mysql -uroot -p
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '000000' WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES;

-- '@' 后面可指定 IP 段:
--   'root'@'%'             任意 IP
--   'root'@'192.168.1.%'   192.168.1.0/24
--   'root'@'10.0.0.50'     指定 IP
```

## MySQL 8.x(语法有变化)

MySQL 8 移除了 `IDENTIFIED BY 'password'` 子句在 GRANT 中的用法,**必须分两步**:

```sql
-- 1. 先创建用户(CREATE USER)
CREATE USER 'root'@'%' IDENTIFIED BY '000000';

-- 2. 再授权(GRANT)
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

## 修改密码(MySQL 5.7+)

```sql
-- MySQL 5.7+
ALTER USER 'root'@'%' IDENTIFIED BY 'newpass';

-- MySQL 5.6
SET PASSWORD FOR 'root'@'%' = PASSWORD('newpass');

-- 通用(所有版本,直接改表)
UPDATE mysql.user SET authentication_string = PASSWORD('newpass') WHERE User = 'root' AND Host = '%';
FLUSH PRIVILEGES;
```

## 验证

```sql
-- 查看用户的 host 字段
SELECT User, Host FROM mysql.user WHERE User = 'root';
-- +------+-----------+
-- | User | Host      |
-- +------+-----------+
-- | root | localhost |
-- | root | %         |       ← 远程登录用
-- +------+-----------+
```

## 远程登录失败的排查

| 问题 | 排查 |
| --- | --- |
| 防火墙挡了 | `firewall-cmd --add-port=3306/tcp`,或 `iptables -I INPUT -p tcp --dport 3306 -j ACCEPT` |
| MySQL 只监听 127.0.0.1 | `my.cnf` 里 `bind-address = 0.0.0.0` 后重启 |
| 密码认证插件不对(MySQL 8) | `ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '000000';` |
| SELinux 挡了 | `setsebool -P mysql_connect_any 1` |
| 报 `Host 'xxx' is not allowed to connect` | 没创建 `'root'@'%'`,执行上面的授权 |

## 相关笔记
- [[mysql 8 给予root用户远程登录权限]]
- [[修改密码]]
- [[数据库配置修改]]
- [[MOC-数据库]]

