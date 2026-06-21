# kvm运维

KVM 虚拟机的日常运维管理命令。

```bash
virsh  snapshot-list <domain>
#查看主机的快照列表
virsh snapshot-create-as compute openstack_ok
#创建快照并指定快照名称
virsh autostart <domian>
#设置kvm启动时虚拟机也启动

#创建一个win2008并且磁盘格式为qcow2 网络为virtio
#注意--disk 顺序 镜像和驱动顺序不要乱
virt-install -n win2008R2 --vcpus=2 --ram=4000 --os-type=windows --os-variant=win2k8 \
--disk path=test2.img,format=qcow2,device=disk,bus=virtio,size=20 \
--disk path=WindowsServer2008R2.iso,device=cdrom,bus=ide \
--disk path=virtio-win-0.1.229.iso,device=cdrom,bus=ide  \
--graphics vnc,listen=0.0.0.0  \
--network network=default,model=virtio  --check path_in_use=off

#关闭虚拟机
virsh destroy win10
#删除虚拟机
virsh undefine win10
#删除虚拟机磁盘文件
rm -rf /opt/abc/win10.qcow2

```

**定制镜像压缩**
用图形界面的虚拟机管理器制作的Windows镜像，文件大小即为新建虚拟机定义的磁盘大小，如果要c盘40G的话，生成的镜像文件就是40G大小，需要压缩后再上传。40G大小压缩后3G。

```text
virt-sparsify --compress --convert qcow2 /var/lib/libvirt/images/Windows10.qcow2 /var/lib/libvirt/images/Windows10.qcow2
#(该命令包含在这些包里guestfish libguestfs-tools)
#上面命令压缩未成功，使用下面命令压缩
qemu-img convert -c -O qcow2 /var/lib/libvirt/images/win10.qcow2 /var/lib/libvirt/images/Windows10.qcow2

virt-filesystems --long -h --all -a /root/Windows10.qcow2
#查看分区情况

qemu-img convert -f qcow2 -O raw CentOS-6.9.qcow2 CentOS-6.9.raw
#qcow2转成raw：将CentOS-6.9.qcow2转为 CentOS-6.9.raw
```
