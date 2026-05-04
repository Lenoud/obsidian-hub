# MySQL 数据库导入与导出

MySQL 数据库导入导出操作的常用方法汇总。

## 导入数据库

### 方法一：source 命令

```bash
# 先创建空数据库
mysql -uroot -p -e "create database abc;"

# 进入 MySQL 后导入
mysql -uroot -p
```

```sql
use abc;
set names utf8;
source /home/abc/abc.sql;
```

### 方法二：命令行直接导入

```bash
mysql -u用户名 -p密码 数据库名 < 数据库名.sql
```

## 导出数据库

```bash
# 导出数据和表结构
mysqldump -u用户名 -p密码 数据库名 > 数据库名.sql

# 导出所有数据库
mysqldump -u用户名 -p --all-databases > 文件名.sql

# 只导出表结构
mysqldump -u用户名 -p密码 -d 数据库名 > 数据库名.sql
```
