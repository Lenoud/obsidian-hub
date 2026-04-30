# iptables

Linux iptables 防火墙规则配置与管理。

###  firewalld 和 iptables 两种不同的防火墙规则的配置方式，不能同时启动。
防火墙是层层过滤的，从第一条开始匹配，到最后一条如果中途匹配成功就不会往下匹配，如果所有规则都不成功就匹配预设规则（保底规则）

CentOS 7 中没有 service iptables save 指令来保存防火墙规则，怎么处理的呢？

解决办法：

```shell
systemctl stop firewalld             # 关闭防火墙
yum -y install iptables-services     # 安装 iptables 服务
systemctl enable iptables            # 设置 iptables 服务开机启动
systemctl start iptables             # 启动 iptables 服务
service iptables save                # 保存 iptables 配置
iptables-save												 # 保存 iptables 配置
service iptables restart             # 重启 iptables 服务
```

### iptables语法
+ iptables(选项)(参数)

#### 选项
+ **-t<表>**：指定要操纵的表；
+ -n：不要把端口或IP反向解析成名称
+ **-A**：向规则链中添加条目；
+ **-D**：从规则链中删除条目；
+ **-i**：向规则链中插入条目；
+ **-R**：替换规则链中的条目；
+ **-L**：显示规则链中已有的条目；
+ **-F**：清楚规则链中已有的条目；
+ **-Z**：清空规则链中的数据包计算器和字节计数器； （防火墙会记录通过了多少数据包）
+ **-N**：创建新的用户自定义规则链；
+ **-P**：定义规则链中的默认目标；
+ **-h**：显示帮助信息；
+ **-p**：指定要匹配的数据包协议类型；
+ **-s**：指定要匹配的数据包源ip地址；
+ **-j<目标>**：指定要跳转的目标；
+ **-i<网络接口>**：指定数据包进入本机的网络接口；
+ **-o<网络接口>**：指定数据包要离开本机所使用的网络接口。
+  **--line-number **：显示行号
+ --sport：源端口
+ --dport：目标端口

#### iptables命令选项输入顺序
+ iptables -t 表名 <-A/I/D/R> 规则链名 [规则号] <-i/o 网卡名> -p 协议名 <-s 源IP/源子网> --sport 源端口 <-d 目标IP/目标子网> --dport 目标端口 -j 动作

+ [root@tp ~]# iptables -F(清除预设表filter中的所有规则链的规则)
+ [root@tp ~]# iptables -X(清除预设表filter中使用者自定链中的规则)
+ [root@tp ~]# iptables -L -n  --line-number（列出所有规则）-t nat （指定要查看的表）

### 设定预设规则
+ [root@tp ~]# iptables -P INPUT  DROP
+ [root@tp ~]# iptables -P OUTPUT  ACCEPT
+ [root@tp ~]# iptables -P FORWARD  DROP

上面的意思是，不符合(INPUT、FORWARD)这两个规则里的数据包就作DROP(放弃)处理，这是控制流入数据包的安全配置。而不符合(OUTPUT)这条规则的数据包就作ACCEPT(通过)处理，这是控制流出数据包的安全配置。

可以看出INPUT，FORWARD两个链采用的是允许什么包通过，而OUTPUT链采用的是不允许什么包通过。这样设置还是挺合理的，当然你也可以三个链都DROP，但这样做我认为是没有必要的，而且要写的规则就会增加，但如果你只想要有限的几个规则时，如只做WEB服务器，还是推荐三个链都是DROP。

**特别注意：如果你是远程SSH登陆来操作的话，当你输入上述第一个命令**iptables -P INPUT DROP**，回车的时候就会自动退出远程，因为你还没有设置任何规则。所以务必先别设定预设规则，要添加完规则之后，再去设定预设规则。一旦被迫退出远程怎么办？如果是云主机，一般可以在管理后台通过VNC登录到服务器。不行的话，唯有联系服务商处理了。**

### 添加iptables规则

允许已建立的或相关链的通行

（比如说ssh连接上服务器，关闭了22端口，ssh会断开，但是输入下面的命令就可以让以建立的连接不断开）

iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

ESTABLISHED  	表示已经通过“握手”建立的连接。
RELATED			表示由已建立的连接（ESTABLISHED）产生的一个新的连接。

#在防火墙上配置FORWARD链，拒绝任何报文通过，此时内外网都无法访问

[root@li~]# iptables -A FORWARD -j REJECT

首先添加INPUT链，INPUT链的默认规则是DROP，所以我们就写需要ACCETP(通过)的链。

为了能采用远程SSH登陆，我们要开启22端口。

+ [root@tp ~]# iptables -A INPUT -p tcp --dport 22 -j ACCEPT

**注意：如果你把预设规则OUTPUT设置成DROP的话**iptables -P OUTPUT DROP**，就要写上下面这条规则（好多人因为没有写这一条规则，导致始终无法SSH）：**

+ [root@tp ~]# iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT

**其他的端口也一样，如果开启了web服务器，OUTPUT设置成DROP的话，同样也要添加一条规则：**

+ [root@tp ~]# iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT

如果做了WEB服务器，开启80端口：

+ [root@tp ~]# iptables -A INPUT -p tcp --dport 80 -j ACCEPT

如果做了邮件服务器,开启25、110端口：

+ [root@tp ~]# iptables -A INPUT -p tcp --dport 110 -j ACCEPT
+ [root@tp ~]# iptables -A INPUT -p tcp --dport 25 -j ACCEPT

如果做了FTP服务器，开启20、21端口：

+ [root@tp ~]# iptables -A INPUT -p tcp --dport 21 -j ACCEPT
+ [root@tp ~]# iptables -A INPUT -p tcp --dport 20 -j ACCEPT

如果做了DNS服务器，开启53端口：

+ [root@tp ~]# iptables -A INPUT -p tcp --dport 53 -j ACCEPT

如果你还做了其他的服务器，需要开启哪个端口，照写就行了。

上面主要写的都是INPUT规则，凡是不在上面的规则里的，都作DROP(不通过)处理。

允许icmp包通过，也就是允许ping：

+ [root@tp ~]# iptables -A OUTPUT -p icmp -j ACCEPT (OUTPUT设置成DROP的话)
+ [root@tp ~]# iptables -A INPUT -p icmp -j ACCEPT (INPUT设置成DROP的话)

允许**loopback**!(不然会导致DNS无法正常关闭等问题)：

+ iptables -A INPUT -i lo -p all -j ACCEPT (如果是INPUT DROP)
+ iptables -A OUTPUT -o lo -p all -j ACCEPT(如果是OUTPUT DROP)

下面写OUTPUT链，OUTPUT链默认规则是ACCEPT，所以我们就写需要DROP(放弃)的链。

减少不安全的端口连：

+ [root@tp ~]# iptables -A OUTPUT -p tcp --sport 31337 -j DROP
+ [root@tp ~]# iptables -A OUTPUT -p tcp --dport 31337 -j DROP

有些木马会扫描端口31337到31340(即黑客语言中的 elite 端口)上的服务。既然合法服务都不使用这些非标准端口来通信，阻塞这些端口能够有效地减少你的网络上可能被感染的机器和它们的远程主服务器进行独立通信的机会。

还有其他端口也一样，像31335、27444、27665、20034 NetBus、9704、137-139（smb）、2049(NFS)端口也应被禁止，我在这写的也不全，有兴趣的朋友应该去查一下相关资料。

当然出于更安全的考虑你也可以把OUTPUT链设置成DROP，那你添加的规则就多一些，就像上边添加允许SSH登陆一样，照着写就行了。

# 保存iptables规则

systemctl mask firewalld

# 重启iptables服务

service iptables stop

service iptables start

#查看当前规则：

cat  /etc/sysconfig/iptables
