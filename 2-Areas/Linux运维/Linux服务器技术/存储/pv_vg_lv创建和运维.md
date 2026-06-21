# pv_vg_lv创建和运维

PV(physical volume)：物理卷在逻辑卷管理系统最底层，可为整个物理硬盘或实际物理硬盘上的分区。

VG(volume group)：卷组建立在物理卷上，一卷组中至少要包括一物理卷，卷组建立后可动态的添加卷到卷组中，一个逻辑卷管理系统工程中可有多个卷组。

LV(logical volume)：逻辑卷建立在卷组基础上，卷组中未分配空间可用于建立新的逻辑卷，逻辑卷建立后可以动态扩展和缩小空间。

PE(physical extent)：物理区域是物理卷中可用于分配的最小存储单元，物理区域大小在建立卷组时指定，一旦确定不能更改，同一卷组所有物理卷的物理区域大小需一致，新的pv加入到vg后，pe的大小自动更改为vg中定义的pe大小。

LE(logical extent)：逻辑区域是逻辑卷中可用于分配的最小存储单元，逻辑区域的大小取决于逻辑卷所在卷组中的物理区域的大小。

卷组描述区域：卷组描述区域存在于每个物理卷中，用于描述物理卷本身、物理卷所属卷组、卷组中逻辑卷、逻辑卷中物理区域的分配等所有信息，它是在使用pvcreate建立物理卷时建立的。

#让内核重新读取分区

partx /dev/sdb

```text
#创建物理卷
pvcreate /dev/sdb1 /dev/sdb2
#查看PV简要信息
pvs
#查看PV详细信息
pvdisplay
#扫描物理卷
pvscan
#移除物理卷
pvremove /dev/sdb2
```

```text
#创建卷组
vgcreate 	 myvg  /dev/sdb1

#查看VG简要信息
vgs

#查看VG详细信息
vgdisplay

#删除vg
vgremove  myvg

#创建vg并指定PE大小为16M
vgcreate -s 16m myvg  /dev/sdb[1-2]

```

## vg扩容
```text
#卷组扩容(将pv并入到vg卷里)
vgextend myvg /dev/sdb3
```

## vg缩容
```text
#卷组中移除PV（缩容）
pvmove /dev/sdb1 #先转移数据
vgreduce myvg /dev/sdb1 #从myvg 中删除/dev/sdb1
```

```text
#创建逻辑卷
lvcreate -L+100M --name mylv1  myvg
sudo lvcreate -l 100%FREE -n my_lv my_vg

#查看LV简要信息
lvs

#查看LV详细信息
lvdisplay

#扫描逻辑卷
lvscan

#格式化逻辑卷
mkfs.etx4  /dev/mapper/myvg-myvg1

#挂载
mount /dev/mapper/myvg-myvg1 /mnt/

#卷组扩容
lvextend -L+100M  /dev/mapper/myvg-myvg1

#操作完成后挂载信息不会变化(df -Th)，需要对文件系统进行扩容 (也可以添加 -r 同时调整文件系统的大小)
resize2fs /dev/mapper/myvg-myvg1

#将所有空间并入逻辑卷
lvextend -r -l +100%FREE  /dev/mapper/myvg-myvg1 (-r 同时调整文件系统的大小)
```

## LV缩容
```text
#逻辑卷的缩容不能在线缩容（必须umount后）
#命令收缩逻辑卷的大小可能会删除逻辑卷上已有的数据，所以再操作前必须进行确认，lvreduce不能在线操作
umount  /dev/mapper/myvg-myvg1

#缩减100m
lvreduce -L -100m  /dev/mapper/myvg-myvg1

#重新挂载文件系统（注意一定要和之前的文件系统相同，不然数据会丢失）
mkfs.ext4  /dev/mapper/myvg-myvg1
mount  /dev/mapper/myvg-myvg1 /mnt
```

## LV扩容
```text
#一块新的磁盘加入机器中并分区
fdisk /dev/sddl
#创建一个pv卷
pvcreate /dev/sdd1
#扩容已有的vg02
vgextend vg02 /dev/sdd1
#将所有的空间重新扩容给lv02
lvextend -r -l +100%FREE  /dev/mapper/vg02-lv02
lvextend -L+100M  /dev/mapper/myvg-myvg1
```

###

---
