# 修改Linux的网卡名称为eth开头

将 Linux 网卡名称修改为传统 eth0 格式。

```bash
#1、编辑 grub 配置文件
vim /etc/sysconfig/grub
# 其实是/etc/default/grub的软连接
# 为GRUB_CMDLINE_LINUX变量增加2个参数，具体内容如下：  net.ifnames=0 biosdevname=0

GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=cl/root rd.lvm.lv=cl/swap net.ifnames=0 biosdevname=0 rhgb quiet"
#2、重新生成 grub 配置文件
grub2-mkconfig -o /boot/grub2/grub.cfg
#然后重新启动 Linux 操作系统，通过 ip addr 可以看到网卡名称已经变为 eth0 。
#3、修改网卡配置文件
#原来网卡配置文件名称为 ifcfg-ens33，这里需要修改为 ethx 的格式，并适当调整网卡配置文件。
mv /etc/sysconfig/network-scripts/ifcfg-ens33  /etc/sysconfig/network-scripts/ifcfg-eth0
# 修改ifcfg-eth0文件如下内容(其它内容不变)
NAME=eth0
DEVICE=eth0
#4、重启网络服务
[root@localhost ~]# systemctl restart network.service
```
