# MySQL 5 给予 root 用户远程登录权限

在 MySQL 5.x 版本中为 root 用户授予远程登录权限。

```sql
mysql -uroot -p000000 -e "grant all privileges on *.* to root@'%' identified by '000000';"
```
