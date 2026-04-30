# ESXi常用命令介绍

1）vmware -v #查看系统版本，例子：

[root@localhost:~] vmware -v

VMware ESXi 6.0.0 build-3620759

2）esxcli system version get #查看系统版本包括patch等信息，例子：

```bash
[root@localhost:~] esxcli system version get
Product: VMware ESXi
Version: 6.0.0
Build: Releasebuild-3620759
Update: 2
Patch: 34
```

3）esxcli system time get #查看系统时间，例子：

```bash
[root@localhost:~] esxcli system time get
2016-09-13T02:02:39Z
```

4）esxcli system time set #修改系统时间，例子：

Cmd options:

-d|–day= Day

-H|–hour= Hour

-m|–min= Minute

-M|–month= Month

-s|–sec= Second

-y|–year= Year

```bash
[root@localhost:~] esxcli system time set -y=2016 -M=9 -d=13 -H=10 -m=9
[root@localhost:~] esxcli system time get
```

2016-09-13T10:09:27Z

5）esxcli system maintenanceMode set --enable true/false

#ESXi主机进入/退出，维护模式，例子：

```bash
[root@localhost:~] esxcli system maintenanceMode set --enable true
[root@localhost:~] esxcli system maintenanceMode get //查看维护模式的状态
Enabled
[root@localhost:~] esxcli system maintenanceMode set --enable false
[root@localhost:~] esxcli system maintenanceMode get
Disabled
esxcli system maintenanceMode set --enable yes # 将ESXi主机进入到维护模式
esxcli system maintenanceMode set --enable no # 将ESXi主机退出维护模式
```

```bash
6) esxcli system shutdown reboot/poweroff
#系统重启/关机（必须处于维护模式，否则命令不生效）
```

7）esxcli network ip interface ipv4 get

#查看接口ipv4地址，例子：

```bash
[root@localhost:~] esxcli network ip interface ipv4 get
Name IPv4 Address IPv4 Netmask IPv4 Broadcast Address Type DHCP DNS
vmk0 10.1.98.165 255.255.255.0 10.1.98.255 STATIC false
```

8）esxcli network ip route ipv4 list

#查看路由表，例子：

[root@localhost:~] esxcli network ip route ipv4 list

Network Netmask Gateway Interface Source

default 0.0.0.0 10.1.98.254 vmk0 MANUAL

10.1.98.0 255.255.255.0 0.0.0.0 vmk0 MANUAL

9）esxcli network nic list

#查看ESXi主机网卡列表（nic）或up-link列表，例子：

[root@localhost:~] esxcli network nic list

Name PCI Device Driver Admin Status Link Status Speed Duplex MAC Address MTU Description

vmnic0 0000:03:00.0 e1000e Up Up 1000 Full 00:50:56:9d:bd:b7 1500 Intel Corporation 82574L Gigabit Network Connection

vmnic1 0000:0b:00.0 e1000e Up Up 1000 Full 00:50:56:9d:7c:7f 1500 Intel Corporation 82574L Gigabit Network Connection

10）esxcli network nic down/up -n=vmnic1

#关闭/打开vmnic1接口

11）esxcli storage core device list

#查看磁盘列表

12）查看主机硬件信息：

vim-cmd hostsvc/hosthardware

关于esxi常用命令总结：

1、重启所有的服务：

services.sh restart

2、重启管理服务：

```bash
/etc/init.d/hostd restart
/etc/init.d/vpxa restart
3、查看服务器IP信息：esxcli network ip interface ipv4 get
4、查宿主机下每个对应的mac：net-stats -l
5、查看网卡状态：esxcfg-vmknic -l
6、esxcfg-info -a # 显示所有ESX相关信息
7、esxcfg-info -w # 显示esx上硬件信息
8、service mgmt-vmware restart # 重新启动vmware服务
9、esxcfg-vmknic -l # 查看宿主机IP地址
10、esxcli system settings advanced list -d # 列出ESXi主机上被改动过的高级设定选项
11、esxcli system settings kernel list -d # 列出ESXi主机上被变动过的kernel设定部分
12、esxcli system snmp get | hash | set | test # 列出、测试和更改SNMP设定
13、用于管理虚拟机的命令总结：
vim-cmd hostsvc/hostsummary # 查看宿主机摘要信息
vim-cmd vmsvc/get.datastores # 查看宿主存储空间信息
vim-cmd vmsvc/getallvms # 列出所有虚拟机
vim-cmd vmsvc/power.getstate VMI # 查看指定VMI虚拟状态
vim-cmd vmsvc/power.shutdown VMI # 关闭虚拟机
vim-cmd vmsvc/power.off VMI # 如果虚拟机没有关闭，使用poweroff命令
vim-cmd vmsvc/get.config VMI # 查看虚拟机配置信息
vim-cmd vmsvc / getallvms #特定主机上运行虚拟机的信息
vim-cmd vmsvc /power.getstate # 使用此命令查看VM是否正在运行，输入vmid代替#
vim-cmd vmsvc /power.on/off # 开启或关闭指定虚拟机，使用vmid代替#
vim-cmd vmsvc /power.reset # 重置虚拟机
vim-cmd vmsvc /power.shutdown # 关闭特定虚拟机
vim-cmd vmsvc /power.reboot # 重启虚拟机
vim-cmd vmsvc /get.summary # 提供有关虚拟机的信息
esxcli vm process list #列出运行虚拟机以及它们的world ID
esxcli vm process kill -type=[soft,hard,force] -world-id=WorldID 提供了许多选项来停止具有特定WorldID的VM。
Soft-正常关机
Hard-立即关机
Force-仅在重启主机时使用强制
vim-cmd solo /registervm /vmfs/vol/datastore/dir/vm.vmx
#在虚拟机管理清单中注册VM并为其分配Vmid。
vim-cmd vmsvc /unregistervm vmid # 从清单中删除虚拟机
```

14、esxcli命令总结：

esxcli software vib install -d /vmfs/volumes/datastore/patches/xxx.zip # 为ESXi主机安装更新补丁和驱动

esxcli network nic list # 列出当前ESXi主机上所有NICs的状态

esxcli network vm list # 列出虚拟机的网路信息

esxcli storage nmp device list # 理出当前NMP管理下的设备satp和psp信息

esxcli storage core device vaai status get # 列出注册到PS设备的VI状态

esxcli storage nmp satp set --default-psp VMW_PSP_RR --satp xxxx # 利用esxcli命令将缺省psp改成Round Robin

esxcli hardware cpu list # cpu信息 Brand，Core Speed，

esxcli hardware cpu global get # cpu信息 （CPU Cores）

esxcli hardware memory get # 内存信息 内存 Physical Memory

esxcli hardware platform get # 硬件型号，供应商等信息,主机型号,Product Name 供应商,Vendor Name

esxcli storage vmfs extent list # 虚拟机使用的存储卷

添加主机进VSAN集群时，报“无法将 vSAN 主机移至目标群集: vSAN 群集的 UUID 不匹配 (主机: 5223a6c9-cf94-f978-1abb-9906506626be，目标: 523ae663-623b-e2fc-39e3-43b15c5ca801)。”错误 。

原因分析：是因为该esxi主机已经加入过其它集群，和现在新加入的集群UUID冲突了，需要该esxi主机先退出旧集群，然后再加入新集群。

查看VSAN集群状态：

执行命令：esxcli vsan cluster get

退出VSAN集群：

执行命令： esxcli vsan cluster leave

加入VSAN集群：

执行命令： esxcli vsan cluster join -u 523ae663-623b-e2fc-39e3-43b15c5ca801

Linux shell命令

首先，让我们了解一些常见的Linux shell命令。这些命令并非ESXi的专用命令，你会发现很多命令也可以在大多数的Linux发行版中使用。

find/cat/grep –在试图查找指定的文件或者在某个文件中查找字符串时这三个命令非常重要。find命令可以基于文件名或者模式查找指定的文件，cat命令用于显示文件内容，grep用于在单个或多个文件内查找指定的字符串。

使用举例：

find /path/to/vm/folder –i name “delta”

#列出虚拟机所有的增量磁盘。

cat hostd.log | grep error

#在hostd.log中查找所有的"error"字符串。

head/tail –查看文件内容时这两个命令非常有用。尽管可以使用cat命令显示文件完整的内容，但head以及tail命令可以用于显示文件开头或结尾的部分，忽略了文件的中间内容。进行故障诊断时tail命令尤为有用，尤其是可以使用-f标记实时监控日志文件发生的变化。

tail -f /var/log/vmkernel.log

#实时查看vmkernel日志发生的变化。

less –显示大文件内容时less命令非常有用。通过在cat命令的输出内容之后输入“|”less,可以分页显示输出结果，而且可以向前或向后滚动浏览。

cat /var/log/vpxa.log | less

#在屏幕上分页显示vpxa.log文件的内容。

df/vdf #这两个命令显示文件系统的可用空间。df命令显示本地文件系统以及数据存储的容量、已用空间以及可用空间。为查看ESXi主机不同随机磁盘的使用情况，必须使用vdf命令。这两个命令都可以用于发现由于可用空间不足而可能导致的任何问题。

ps/kill –这两个命令分别用于查找ESXi主机内部运行的服务、强制终止这些服务。ps命令包括很多命令行开关，但最常用的是检索正在运行的进程的ID，然后就可以使用Kill命令终止相应的服务。

vi – 如果之前不熟悉vi命令，那么在学习时大多会遇到麻烦。Vi是一个文本编辑器，用于修改文件内容—vSphere管理员通过命令行shell进行故障诊断时必须要具备该技能。

ESXi专用命令

接下来让我们了解一些在ESXi命令行shell下最常用的命令。这些ESXi命令不仅能够帮助你进行故障诊断，还可以用于日常维护以及性能监控。

services.sh – Linux服务通常使用services命令管理，管理ESXi服务是通过使用services.sh命令实现的。Services.sh命令支持的参数包括stop、start、restart，通过这三个参数可以停止、启动或重启所有的ESXi服务。

services.sh restart – 重启所有的ESXi服务

/etc/init.d – 执行位于/etc/init.d目录下的脚本可以启动或停止对应的服务。如果只想重启vCenter Server Agent（vpax服务），可以运行/etc/init.d/vpxa restart 命令。而services.sh restart将重启所有服务。

/etc/init.d/vpxa restart – 重启主机上的 vCenter Agent

cat /etc/chkconfig.db – 查看所有ESXi服务的运行状态。

vmkping –我们都熟悉ping命令的用法及功能。Vmkping命令更进一步，允许使用Vmkernel的IP堆栈通过特定的接口发送ICMP数据包。这意味着你可以通过vMotion网络而非管理网络发送ping包。

vmkping –I vmk1 10.10.10.1 – 通过vmkl接口向10.10.10.1发送ICMP请求

nc –组合使用vmkping、nc命令（netcat），可以确认ESXi主机与特定IP之间的网络连通性。尽管vmkping命令通过ICMP确认连通性，但有时我们想确认是否可以访问特定的TCP端口(例如iSCSI的TCP端口是3260)。

nc –z 10.10.10.10 3260 – 测试是否能够访问10.10.10.10的3260端口。

Vmkfstools -如果需要通过命令行管理VMFS数据卷以及虚拟磁盘，那么vmkfstools命令就派上用场了。使用vmkfstools命令可以创建、克隆、扩展、重命名并删除VMDK文件。除了虚拟磁盘选项，你还可以使用vmkfstools命令创建、扩展、增大、回收文件系统的数据块。

vmkfstools –i test.vmdk testclone.vmdk

– 将test.vmdk克隆为testclone.vmdk

esxtop –对ESXi主机进行性能监控以及故障诊断时，很少有工具能够提供和esxtop同样多的信息。除提供和Linux top命令类似的功能外，esxtop还可以收集很多VMware专有的指标，包括中断、内存、网络、磁盘适配器、磁盘设备以及电源管理。

vscsiStats – 需要进一步监控存储I/O的性能时，vscsiStats命令就能够派上用场了。vscsiStats命令能够帮助你收集与虚拟机磁盘I/O负载相关的性能数据。进行容量规划或者迁移后端存储时，使用vscsiStats命令收集到的数据可谓价值连城。

vim-cmd –vim-cmd是构建在hostd进程之上的命令空间，允许最终用户调用几乎所有的vSphere API。Vim-cmd提供了一些ESXi子命令管理不同的虚拟基础设施，而且和vimsh相比，更容易使用。

dcui –登录到ESXi主机时，VMware直接用户控制台接口（DCUI）提供了基于菜单的主机管理功能。DCUI提供了很多不同的功能，比如root密码维护、网络维护。有时你可能只能通过SSH访问主机，但幸运的是，在命令行下执行dcui命令就可以进入基于菜单的DCUI系统。

vm-support –收集ESXi主机所有的支持及日志信息。

下面列出了使用最频繁（肯定不是所有）的命名空间：

esxcli hardware – 想获取ESXi主机的硬件及配置信息时，esxcli硬件命名空间就能够派上用场了。

esxcli hardware cpu list – 获取CPU信息（系列、型号以及缓存）

esxcli hardware memory get – 获取内存信息（可用内存以及非一致内存访问）

esxcli iscsi – iscsi命名空间可以被用于监控并管理硬件iSCSI及软件iSCSI设置。

esxcli iscsi software –用于启用/禁用软件iSCSI initiator。

esxcli iscsi adapter –用于设置软硬件iSCSI适配器的发现、CHAP以及其他设置

esxcli iscsi sessions – 用于列出主机上已建立的iSCSI会话。

esxcli network –需要监控vSphere网络并对如下网络组件进行调整时，包括虚拟交换机、VMkernel网络接口、防火墙以及物理网卡等，esxcli网络命名空间就派上用场了。

esxcli network nic –列出并修改网卡信息，比如名字、唤醒网卡以及速度。

esxcli network vm list – 列出有一个活动网络端口的虚拟机的网络信息。

esxcli network vswitch –检索并管理VMware的标准交换机以及分布式虚拟交换机。

esxcli network ip – 管理VMkernel端口，包括管理、vMotion以及FT网络。还可以修改主机的所有IP栈，包括DNS、IPsec以及路由信息。

esxcli software – 软件命名空间可以用于检索ESXi主机已安装的软件及驱动并可以安装新组件。

esxcli software vib list – 列出ESXi主机上已经安装的软件及驱动。

esxcli storage – 可能是最常用的esxcli命令命名空间之一，包括了管理连接到vSphere的存储的所有信息。

esxcli storage core device list – 列出当前存储设备

esxcli storage core device vaai status get –获得存储设备支持的VAAI的当前状态。

esxcli system – 通过该命令使你能够控制ESXi的高级选项，比如设置syslog并管理主机状态。

esxcli system maintenanceMode set –enabled yes/no – 将主机设置为维护模式

查看并更改ESXi高级设置（提示：使用esxcli system settings

advanced list –d 命令查看非默认设置）

esxcli system syslog –查看 Syslog 及配置信息

esxcli vm – ESXi的虚拟机命名空间用于列出运行在主机上的虚拟机的各种信息，如果需要可以强制关闭这些虚拟机。

esxcli vm process list –列出已启动的虚拟机的进程信息。

esxcli vm process kill – 停止正在运行的虚拟机的进程，关闭虚拟机或者强制关闭虚拟机电源。

esxcli vsan – ESXi的VSAN命名空间包括配置并维护VSAN的很多命令，包括数据存储、网络、默认域名以及策略配置。

esxcli vsan storage – 配置VSAN使用的本地存储，包括增加、删除物理存储并修改自动声明。

esxcli vsan cluster – 本地主机脱离/加入VSAN集群。

esxcli esxcli – esxcli命令包括一个称为esxcli的命名空间，通过使用esxcli命名空间，你可以获得更多信息。

esxcli esxcli command list – 列出所有的esxcli命令及其提供的功能
