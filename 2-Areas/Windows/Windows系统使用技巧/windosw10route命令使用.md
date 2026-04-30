# Windows 10 Route 命令使用

Windows 系统下 route 命令用于查看和修改本地 IP 路由表。

## 常用命令

```cmd
# 查看路由表
route print
route PRINT -4
route PRINT -6
route PRINT 157*              # 只打印匹配 157* 的项

# 添加路由
route ADD 157.0.0.0 MASK 255.0.0.0 157.55.80.1 METRIC 3 IF 2
route ADD 3ffe::/32 3ffe::1

# 修改路由（只用于修改网关和/或跃点数）
route CHANGE 157.0.0.0 MASK 255.0.0.0 157.55.80.5 METRIC 2 IF 2

# 删除路由
route DELETE 157.0.0.0
route DELETE 3ffe::/32
```

## 实用示例

```cmd
# 修改 metric，值越小优先级越高
route add 0.0.0.0 mask 0.0.0.0 192.168.1.1 metric 5

# 添加永久路由（-p 参数）
route -p add 0.0.0.0 mask 0.0.0.0 192.168.1.1 metric 5

# 删除路由
route delete 192.168.1.0
route delete 192.168.1.0 mask 255.255.255.0 192.168.1.1
```
