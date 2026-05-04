# MySQL 导入宝塔文件报错

导入宝塔导出的 SQL 文件时，因文件过大导致报错，需修改 MySQL 配置中的 `max_allowed_packet` 参数。

## 解决方法

修改 `my.cnf` 配置文件：

```ini
[mysqld]
max_allowed_packet=1073741824
```

## 说明

| 参数 | 值 | 说明 |
| --- | --- | --- |
| `max_allowed_packet` | `1073741824` | 最大数据包大小，设为 1GB |
