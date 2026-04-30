# redis-aof配置调优

redis-aof配置调优相关技术笔记。

Redis 操作与部署。

**在Redis中，AOF配置为以三种不同的方式在磁盘上执行write或者fsync。假设当前Redis压力过大，请配置Redis不执行fsync。除此之外，避免AOF文件过大，Redis会进行AOF重写，生成缩小的AOF文件。请修改配置，让AOF重写时，不进行fsync操作。**

+ **Always**：每次事件循环都进行一次同步操作
+ **Everysec**：每秒进行一次同步操作
+ **No**：由操作系统控制同步操作

```shell
[root@redis-master ~]# cat /etc/redis.conf |grep appendfsync
appendfsync everysec
no-appendfsync-on-rewrite no
#修改为
[root@redis-master ~]# cat /etc/redis.conf |grep appendfsync
appendfsync no
no-appendfsync-on-rewrite yes

systemctl restart redis
#查询是否生效
[root@redis-master ~]# redis-cli -a 123456 CONFIG GET no-appendfsync-on-rewrite
(1) "no-appendfsync-on-rewrite"
(2) "yes"
[root@redis-master ~]# redis-cli -a 123456 CONFIG GET appendfsync
(1) "appendfsync"
(2) "no"
```

---
