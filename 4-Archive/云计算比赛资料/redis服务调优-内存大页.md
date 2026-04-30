# redis服务调优-内存大页

redis服务调优-内存大页相关技术笔记。

Redis 操作与部署。



使用上一题的环境。因为Redis服务采用了内存大页，生成RDB期间，即使客户端修改的数据只有50B的数据，Redis需要复制2MB的大页。当写的指令比较多的时候就会导致大量的拷贝，导致性能变慢。请修改Redis的内存大页机制，规避大量拷贝时的性能变慢问题。配置完成后提交Redis节点的用户名、密码和IP地址到答题框。

```shell
[root@node1 ~]# echo never > /sys/kernel/mm/transparent_hugepage/enabled
[root@node1 ~]# cat /sys/kernel/mm/transparent_hugepage/enabled
always madvise [never]
```
