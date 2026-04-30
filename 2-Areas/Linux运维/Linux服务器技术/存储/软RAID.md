# 软RAID

安装软件包

yum -y install mdadm

# 安装 RAID 管理工具

raid0创建

[root@localhost ~]#

```bash
#创建一个raid0
mdadm -Cv /dev/md0 -a yes -l 0 -n 2 /dev/sdb1 /dev/sdc1
#创建一个raid1
mdadm -Cv /dev/md2 -l1 -n2 /dev/sdb[1-2]
#创建一个raid5
mdadm -Cv /dev/md1 -l5 -n4 /dev/sdc[1-4] --spare-devices=1 /dev/sdb3
#模拟分区二损坏
mdadm -f /dev/md1 /dev/sdc2
#查看raid详情
mdadm -D /dev/md1
#热移除坏盘
mdadm -r /dev/md1 /dev/sdc2

```

选项	   全称	 作用

-C	--create	创建阵列

-a	--auto	同意创建设备

-l	--level	阵列模式

-n	--reid-devices	阵列中活动磁盘的数目

-x	--spare-devices=N	表示当前阵列中热备盘有 N 块（自定义 N 数量即可）

-S	--stop	关闭阵列（关闭前需先取消挂载）

查看 RAID0 状态

[root@localhost ~]# cat /proc/mdstat									# 查看概要信息

[root@localhost ~]# mdadm -D /dev/md0 								# 查看更详细的信息

Raid Level：阵列级别。

Array Size：阵列容量大小。

Raid Devices：RAID 成员的个数。

Total Devices：RAID 中下属成员的总计个数，因为还有冗余硬盘或分区，也就是 spare。

State：包含三个状态（clean 表示正常，degraded 表示有问题，recovering 表示正在恢复或构建）

Active Devices：被激活的 RAID 成员个数。

Working Devices：正常工作的 RAID 成员个数。

Failed Devices：出问题的 RAID 成员。

Spare Devices：备用 RAID 成员个数（会自动替换出现问题的成员）。

UUID：RAID 的 UUID 值，在系统中是唯一的。

创建 mdadm 配置文件

因为每次系统重启时，RAID 的 UUID 都会改变，所以创建 mdadm 文件就是为了每次重启时自动加载软 RAID。

[root@localhost ~]# echo "DEVICE /dev/sdb1 /dev/sdc1" > /etc/mdadm.conf 	# 指定软 RAID 设备

[root@localhost ~]# mdadm -Ds /dev/md0 >> /etc/mdadm.conf	# 将 RAID0 的 UUID 追加到该目录
