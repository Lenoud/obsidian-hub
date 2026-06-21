# windosw10route命令使用

windosw10route命令使用相关技术笔记。

```bash
#示例:

    > route PRINT
    > route PRINT -4
    > route PRINT -6
    > route PRINT 157*          .... 只打印那些匹配  157* 的项

    > route ADD 157.0.0.0 MASK 255.0.0.0  157.55.80.1 METRIC 3 IF 2
             destination^      ^mask      ^gateway     metric^    ^
                                                         Interface^
      如果未给出 IF，它将尝试查找给定网关的最佳
      接口。
    > route ADD 3ffe::/32 3ffe::1

    > route CHANGE 157.0.0.0 MASK 255.0.0.0 157.55.80.5 METRIC 2 IF 2

      CHANGE 只用于修改网关和/或跃点数。

    > route DELETE 157.0.0.0
    > route DELETE 3ffe::/32

1、查看电脑中的路由的命令：

route print

2、修改“metric”，值越小权限越高：

route add 0.0.0.0 mask 0.0.0.0 192.168.1.1 metric 5

3、如果添加永久生效的路由，则使用“-p”参数：

route -p add 0.0.0.0 mask 0.0.0.0 192.168.1.1 metric 5
#参数改成delete即可删除

4、删除路由：

route delete 192.168.1.0
route delete 192.168.1.0 mask 255.255.255.0 192.168.1.1
```
