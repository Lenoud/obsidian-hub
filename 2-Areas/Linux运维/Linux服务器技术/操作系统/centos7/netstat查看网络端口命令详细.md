# netstat查看网络端口命令详细

该 `netstat` 命令用来显示网络状态。

> 注意：`netstat` 属于 `net-tools` 套件，自 2001 年起已停止维护，CentOS 7+ 默认不再安装。现代 Linux 推荐使用 `ss`（属于 iproute2）替代，例如 `ss -tnlp` 等价于 `netstat -tnlp`。

传统上，它用于问题确定而不是性能测量。但是，`netstat` 命令可用于确定网络上的流量，以确定性能问题是否是由于网络拥塞引起的。

在 `netstat` 的关于所配置的网络接口，诸如以下的流量命令显示信息：

+ 与所有sockets关联的任何协议控制块的地址以及所有sockets的状态
+ 通信子系统中接收，传输和丢弃的数据包数
+ 每个接口的累积统计信息
+ 路线及其状态

**"netstat" 命令参数：**

-r：--route，显示路由表信息
-g：--groups，显示多重广播功能群组组员名单
-s：--statistics，按照每个协议来分类进行统计。默认的显示IP、IPv6、ICMP、ICMPv6、TCP、TCPv6、UDP和UDPv6 的统计信息。
-M：--masquerade，显示网络内存的集群池统计信息
-v：--verbose，命令显示每个运行中的基于公共数据链路接口的设备驱动程序的统计信息
-W：--wide，不截断IP地址
-n：进制使用域名解析功能。链接以数字形式展示(IP地址)，而不是通过主机名或域名形式展示
-N：--symbolic，解析硬件名称
-e：--extend，显示额外信息
-p：--programs，与链接相关程序名和进程的PID
-t：所有的 tcp 协议的端口
-x：所有的 unix 协议的端口
-u：所有的 udp 协议的端口
-o：--timers，显示计时器
-c：--continuous，每隔一个固定时间，执行netstat命令
-l：--listening，显示所有监听的端口
-a：--all，显示所有链接和监听端口
-F：--fib，显示转发信息库(默认)
-C：--cache，显示路由缓存而不是FIB
-Z：--context，显示套接字的SELinux安全上下文

**命令使用举例 ：**

netstat -anp：显示系统端口使用情况
netstat -nupl：UDP类型的端口
netstat -ntpl：TCP类型的端口
netstat -na|grep ESTABLISHED|wc -l：统计已连接上的，状态为"established"
netstat -l：只显示所有监听端口
netstat -lt：只显示所有监听**tcp**端口
