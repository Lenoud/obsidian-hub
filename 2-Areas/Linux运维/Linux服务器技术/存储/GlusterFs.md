# GlusterFs

GlusterFS 分布式文件系统部署与配置。



## centos6.5操作系统
```bash
#禁用防火墙
systemctl stop firewalld
systemctl disable firewalld
setenforce 0
sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config

#下载glusterfs  alist里面有rar文件
yum localinstall *

#分区（略）
fdisk

#创建挂在点
mkdir -p /export/brick1/
mkfs.xfs /dev/sdb1
mount /dev/sdb1 /export/brick1/
mkdir -p /export/brick1/gv0
```

```bash
gluster peer probe 192.168.100.10
#添加节点
gluster volume create gv0 replica 2 ip:/exprot/brick1/gv0 ip2:/export/brick1/gv0
#创建卷
gluster volume start gv0
#启动卷
gluster volume info
#查看信息
mount -t glusterfs gluster_node_ip:/gv0 /mnt/
#其他节点挂载目录

```

gluster 运维操作汇总

```bash

添加节点： gluster peer probe 服务器 IP﻿ 。
删除节点： gluster peers detach 服务器 IP﻿ 。

如果节点上有brick，则需要先删除brick。

查看卷信息： gluster volume info﻿ 。
查看卷状态： gluster volume status﻿ 。
启动卷： gluster volume start 卷名 ﻿ 。
停止卷： gluster volumn stop 卷名 ﻿ 。
修复卷

a. 仅修复有问题的文件： gluster volume heal mamm-volume﻿ 。
b. 修复所有文件： gluster volume heal mamm-volume full﻿ 。
c. 查看自愈详情： gluster volume heal mamm-volume info﻿ 。

添加brick：
gluster volume add-brick gv0 192.168.15.103:/export/brick1/gv0
192.168.15.104:/export/brick1/gv0 。

a需要先添加节点。

9. 移除卷： gluster volume remove-brick gv0 192.168.15.103:/export/brick1/gv
0 192.168.15.104:/export/brick1/gv0 start﻿ 。
a. 若是副本卷/条带卷，则要移除的Brick是Replica/Stripe的整数倍（即成对移除）。
10. 查看移除任务的状态： gluster volume remove-brick gv0 192.168.15.103:/export/
brick1/gv0 192.168.15.104:/export/brick1/gv0 status﻿ 。
11. 移除卷（不进行数据迁移）： gluster volume remove-brick gv0 192.168.15.103:/ex
port/brick1/gv0 192.168.15.104:/export/brick1/gv0 commit﻿
```
