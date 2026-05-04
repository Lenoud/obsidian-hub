# SQL CASE WHEN 匹配结果替换

使用 `CASE WHEN` 语句对查询结果中的字段值进行条件替换。

## 示例

将登录方式和身份的数字代码替换为中文描述：

```sql
CREATE TABLE zp_wolive_login_log_登录日志表
SELECT
    id,
    uid 登陆者ID,
    NAME 登陆者账号,
    ip 登陆者IP,
    area 地址,
    CASE login_side
        WHEN '0' THEN '未知登陆方式'
        WHEN '1' THEN '电脑登录'
        WHEN '2' THEN '手机登录方式'
    END 登录方式,
    CASE source
        WHEN '1' THEN '管理员'
        WHEN '2' THEN '客服人员'
    END 身份,
    FROM_UNIXTIME(createtime, '%Y-%m-%d %T') 登录时间
FROM
    wolive_login_log;
```

## 说明

| 语法 | 说明 |
| --- | --- |
| `CASE 字段 WHEN 值 THEN 结果` | 简单 CASE 表达式，按字段值匹配 |
| `END` | 结束 CASE 语句 |
| `FROM_UNIXTIME()` | 将时间戳转换为日期格式 |
