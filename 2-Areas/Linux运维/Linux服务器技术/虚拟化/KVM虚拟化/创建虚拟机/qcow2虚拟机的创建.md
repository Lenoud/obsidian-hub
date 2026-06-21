# qcow2 虚拟机的创建

qcow2(QEMU Copy-On-Write version 2)是 QEMU/KVM 推荐的虚拟机磁盘格式,支持**精简分配、快照、压缩、加密**。本文记录几种创建 qcow2 虚拟机的方法。

> 手写/截图笔记:`![[1721383126271-*.png]]`、`![[1721383156723-*.png]]`、`![[1721383178765-*.png]]`(下方为文字版完整命令)。

## qcow2 vs raw vs vmdk

| 格式 | 特点 | 适用 |
| --- | --- | --- |
| **qcow2** | 精简分配、快照、加密、压缩 | KVM/PVE 默认,推荐 |
| **raw** | 裸格式,无元数据,性能最高 | 对性能极致要求,不需快照 |
| **vmdk** | VMware 格式 | 跨平台迁移到 ESXi |
| **vhdx** | Hyper-V 格式 | Windows 生态 |

## 1. 创建空磁盘 + virt-install 安装

```bash
# 创建 20G 的 qcow2 磁盘(实际占用接近 0,按需扩展)
qemu-img create -f qcow2 /var/lib/libvirt/images/vm1.qcow2 20G

# 查看磁盘信息
qemu-img info /var/lib/libvirt/images/vm1.qcow2
# image: vm1.qcow2
# file format: qcow2
# virtual size: 20 GiB (21474836480 bytes)
# disk size: 196 KiB        ← 实际占用

# 从 ISO 启动安装
virt-install \
  --name vm1 \
  --vcpus 2 \
  --memory 2048 \
  --disk path=/var/lib/libvirt/images/vm1.qcow2,bus=virtio,format=qcow2 \
  --cdrom /path/to/ubuntu-22.04.iso \
  --network bridge=virbr0,model=virtio \
  --os-variant ubuntu22.04 \
  --graphics vnc,listen=0.0.0.0
```

## 2. 复制现有磁盘(克隆)

```bash
# 用 virt-clone 完整克隆(自动生成新 MAC、改 UUID)
virt-clone \
  --original vm1 \
  --name vm2 \
  --file /var/lib/libvirt/images/vm2.qcow2

# 或直接用 qemu-img(不修改 UUID/MAC,需手动处理)
cp vm1.qcow2 vm2.qcow2
```

## 3. 从模板快速创建(linked clone)

```bash
# 以 base.qcow2 作为后端,创建差异磁盘(节省空间,但依赖后端)
qemu-img create -f qcow2 -F qcow2 -b base.qcow2 vm-derived.qcow2
```

## 4. 快照管理

```bash
# virsh 方式
virsh snapshot-create-as vm1 snap-before-update "升级前备份"
virsh snapshot-list vm1
virsh snapshot-revert vm1 snap-before-update       # 回滚
virsh snapshot-delete vm1 snap-before-update

# qemu-img 方式(对磁盘文件)
qemu-img snapshot -l vm1.qcow2
qemu-img snapshot -c snap1 vm1.qcow2
qemu-img snapshot -a snap1 vm1.qcow2
qemu-img snapshot -d snap1 vm1.qcow2
```

## 5. 磁盘扩容

```bash
# 1. 先关闭虚拟机
virsh destroy vm1

# 2. 扩展 qcow2 文件(20G → 50G)
qemu-img resize /var/lib/libvirt/images/vm1.qcow2 50G

# 3. 启动虚拟机,在 guest 内扩展分区
virsh start vm1
virsh console vm1
# guest 内:
# fdisk /dev/vda  调整分区
# resize2fs /dev/vda1  或  xfs_growfs /
```

## 6. 转换格式

```bash
# qcow2 → raw(用于 ESXi 等不支持 qcow2 的平台)
qemu-img convert -f qcow2 -O raw vm1.qcow2 vm1.raw

# raw → vmdk(VMware)
qemu-img convert -f raw -O vmdk vm1.raw vm1.vmdk

# qcow2 压缩(导出模板时减小体积)
qemu-img convert -c -f qcow2 -O qcow2 vm1.qcow2 vm1-compressed.qcow2
```

## 相关笔记
- [[KVM虚拟化]] — KVM 总览
- [[创建虚拟机]] — virt-install 详解
- [[kvm虚拟化部署]]
- [[PVE虚拟化服务器]]
- [[MOC-Linux运维]]
