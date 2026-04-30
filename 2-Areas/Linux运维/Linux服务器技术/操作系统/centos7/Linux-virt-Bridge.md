# 创建虚拟网桥并且和物理端口绑定

Linux 虚拟网桥（Bridge）配置。
## 环境的配置
bridge-utils 软件包，它提供 brctl 工具来配置网桥

**yum install -y bridge-utils**

net-tool 软件包，它提供ifconfig命令供我们管理网络

**yum install -y net-tool**

关闭网络管理工具NetworkManager，防止虚拟机接口重启网络后IP被解绑

**systemctl stop NetworkManager  **

**systemctl disable NetworkManager**

加载 tun 和 bridge 模块

[root@localhost ~]# **lsmod | grep tun**

tun                    36164  2 vhost_net

[root@localhost ~]# **lsmod | grep bridge**

bridge                151336  1 ebtable_broute

stp                    12976  1 bridge

llc                    14552  2 stp,bridge

**环境配置至此配置完成**

---

---

## 虚拟网桥的添加和配置
先将要绑定的物理接口的IP等配置注释掉，防止重启网卡后绑定信息失效

vi /etc/sysconfig/network-scripts/ifcfg-ens33

**注释掉所有字段，添加如下字段**

NAME="ens33"

DEVICE="ens33"

ONBOOT="yes"

BRIDGE=br0

**brctl addbr br0 **

创建一个名为 br0的虚拟网桥

**brctl addif br0 ens33 	**

把物理接口ens33和网桥br0绑定

**brctl show br0     **

查询网桥状态  输出如下：

bridge name	bridge id		STP enabled	interfaces

br0		8000.eef79549afe7	no		ens33

**编辑ifcfg-br0配置网桥的IP地址**

**vi /etc/sysconfig/network-scripts/ifcfg-br0**

DEVICE="br0k"

ONBOOT="yes"

HOTPLUG="yes"

TYPE="Bridge"

BOOTPROTO="none"

IPADDR="192.168.100.10"

NETMASK="255.255.255.0"

GATEWAY="192.168.100.2"

STP="on"

DELAY="0.0"

---
**开启网络**

ifconfig br0 up

ifconfig ens33 up

**配置完成后重启网络**

**systemctl restart network**

**至此虚拟网桥br0绑定物理端口的配置完成**

---

---

**brctl stp br0 on  **

开启stp协议(生成树协议)   ##可写可不写
