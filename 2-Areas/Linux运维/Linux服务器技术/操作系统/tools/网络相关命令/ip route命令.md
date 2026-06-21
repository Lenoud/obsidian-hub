# ip route 命令

ip route 用于管理 Linux 静态路由表,是 route 命令的替代品。
linux 系统中，可以自定义从 1－252个路由表。其中，linux系统维护了4个路由表：

+ 0#表： 系统保留表
+ 253#表： default table 没特别指定的默认路由都放在该表
+ 254#表： main table 没指明路由表的所有路由放在该表
+ 255#表： local table 保存本地接口地址，广播地址、NAT地址 由系统维护，用户不得更改

查找路由表可以通过ip route show table table_number[table name]命令，路由表和表明的对应关系记录在/etc/iproute2/rt_tables中

ip route命令如果没有明确指定table则使用main table，ip route命令是route命令的替换命令，route命令只能操作main路由表

## ip route 命令格式说明
### ip route add
增加路由

```bash
● ip route add default via 192.168.1.1
● 增加默认网关（在main路由表中）
● ip route add 192.168.4.0/24 via 192.168.166.1 dev wlan0
● 设置192.168.4.0网段的网关为192.168.166.1,数据走wlan0接口
● ip route add 192.168.1.9 via 192.168.166.1 dev wlan0
● 增加目的地址192.168.1.9的网关为192.168.166.1，数据走wlan0接口
● ip route add default via 192.168.1.1 table 1
● 在1号路由表中增加默认网管
● ip route add 192.168.0.0/24 via 192.168.166.1 table 1
● 在1号路由表中增加192.168.0.0网段的网关为192.168.166.1
```

### ip route show
+ ip route 或：ip route show
+ 显示系统路由
+ ip route show table local
+ 查看本地路由表

# **ip route get**
+ ip route get 169.254.0.0/16
+ 获取到目标的单个路由，并按照内核所看到的方式打印其内容

# **ip route delete**
+ ip route del 192.168.4.0/24
+ 删除192.168.4.0网段的网关
+ ip route del default
+ 删除默认网关

# **ip route flush**
+ ip route flush 10.38.0.0/16
+ 删除特定路由
+ ip route flush table main
+ 清空路由表

ip route是route命令的升级版本，但route命令仍在大量使用
