# 扩容centos-root分区

CentOS 7 系统 root 分区扩容操作。

**1.缩容home分区并入root分区**

```bash
#备份home分区文件
tar cvf /tmp/home.tar /home

#下载fuser命令插件
yum -y install psmisc

#终止/home系统文件进程，卸载/home
fuser -km /home/
umount /home

#删除/home对应的lv
lvremove /dev/mapper/centos-home

#扩展/root对应的lv
lvextend -L +10G /dev/mapper/centos-root
#或者将pv剩余空间全部划分给逻辑卷
lvextend /dev/centos/root /dev/sdb1

#扩展/root文件系统
xfs_growfs /dev/mapper/centos-root

#重新创建home的lv
lvcreate -L 36.9G -n /dev/mapper/centos-home

#创建文件系统
mkfs.xfs /dev/mapper/centos-home

#挂载home
mount /dev/mapper/centos-home

```

home文件恢复

```bash
tar xvf /tmp/home.tar -C /home/
cd /home/home/
mv * ../
cd /home
rm -rf home
```

**2.Openstack创建虚拟机计算节点root分区磁盘空间不足导致报错500-LVM重新分区（创建云主机报错500 找不到可用主机 ）**
计算节点先添加一块硬盘然后执行以下命令

将图中第一层分区 运行后n命令指定第几个
# fdisk /dev/sdb

创建物理分区pv
#pvcreate /dev/sdb1

将pv并入卷组资源池
#vgextend centos /dev/sdb1
查看pv
#pvs

查看vg
#vgdisplay

将逻辑卷扩大1t
 #lvextend -L +1000G /dev/centos-root
 或者将pv剩余空间全部划分给逻辑卷
 #lvextend /dev/centos/root /dev/sdb1

 更新资源 针对centos
 #xfs_growfs /dev/centos/root

 查看更新
# df -hl

计算节点root分区扩容成功
