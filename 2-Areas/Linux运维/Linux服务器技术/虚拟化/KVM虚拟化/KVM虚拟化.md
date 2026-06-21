# KVM 虚拟化

KVM(Kernel-based Virtual Machine)是 Linux 内核内建的 Type-1 虚拟化模块,从 Linux 2.6.20 起合入主线。配合 QEMU 提供完整的硬件虚拟化能力,是开源虚拟化的事实标准,OpenStack / Proxmox VE / oVirt 都基于 KVM。

## 架构原理

```text
┌────────────────────────────────┐
│      用户态(虚拟机)            │
│  VM1    VM2    VM3  ...         │
└────┬───────────────────────────┘
     │  ioctl / virtio
┌────▼───────────────────────────┐
│  QEMU(用户态进程,模拟硬件)    │
└────┬───────────────────────────┘
     │
┌────▼───────────────────────────┐
│  KVM 内核模块(kvm.ko + kvm-intel.ko/kvm-amd.ko)│
│  - CPU 虚拟化(Intel VT-x / AMD-V)            │
│  - 内存虚拟化(EPT / NPT)                      │
│  - IRQ 虚拟化(APIC-v / posted interrupts)     │
└────┬───────────────────────────┘
     │
┌────▼───────────────────────────┐
│           Linux 内核             │
└────────────────────────────────┘
```

## 与其他虚拟化的关系

| 平台 | 底层 | 描述 |
| --- | --- | --- |
| **原生 KVM + QEMU** | KVM | 直接用 `qemu-system-x86_64 -enable-kvm` 启动 |
| **libvirt / virsh** | KVM | 提供统一 API 和 CLI 管理虚拟机 |
| **Proxmox VE** | KVM | Web 界面封装,加 LXC 支持 |
| **OpenStack** | KVM | 云平台,管理大规模 KVM 集群 |
| **oVirt / RHV** | KVM | Red Hat 的虚拟化管理平台 |

## 支持检查

```bash
# 1. CPU 是否支持虚拟化(返回非 0 即支持)
egrep -c '(vmx|svm)' /proc/cpuinfo

# 2. 当前是否已加载 KVM 模块
lsmod | grep kvm
# kvm_intel / kvm_amd
# kvm
```

如果 `egrep` 返回 0,需要在 BIOS 中启用 Intel VT-x 或 AMD-V。

## 安装(以 CentOS / Ubuntu 为例)

```bash
# CentOS / RHEL / Rocky
yum install qemu-kvm libvirt virt-install bridge-utils
systemctl enable --now libvirtd

# Ubuntu / Debian
apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virtinst
systemctl enable --now libvirtd

# 把当前用户加入 libvirt 组(免 sudo)
usermod -aG libvirt $USER
```

## 创建虚拟机

### 命令行(virt-install + qcow2)

```bash
# 创建 20G 的 qcow2 磁盘(精简分配)
qemu-img create -f qcow2 /var/lib/libvirt/images/vm1.qcow2 20G

# 安装 Ubuntu 22.04(从 ISO)
virt-install \
  --name vm1 \
  --vcpus 2 \
  --memory 2048 \
  --disk path=/var/lib/libvirt/images/vm1.qcow2,bus=virtio \
  --cdrom /path/ubuntu-22.04.iso \
  --network bridge=virbr0,model=virtio \
  --graphics vnc,listen=0.0.0.0 \
  --os-variant ubuntu22.04
```

### 管理

```bash
virsh list --all                # 列出所有虚拟机
virsh start vm1                 # 启动
virsh shutdown vm1              # 优雅关机
virsh destroy vm1               # 强制断电
virsh autostart vm1             # 开机自启
virsh console vm1               # 文字控制台
virsh dumpxml vm1 > vm1.xml     # 导出配置
```

## 性能优化要点

| 维度 | 推荐配置 | 说明 |
| --- | --- | --- |
| 磁盘 | `virtio` 总线 + `qcow2` | 性能远高于 IDE 模拟 |
| 网卡 | `virtio` 模型 | 性能远高于 e1000 模拟 |
| CPU | `--cpu host-passthrough` | 透传宿主机 CPU 指令集 |
| 内存 | 大页(HugePages) | 减少 TLB miss,提升性能 |
| 存储 | virtio-scsi + io=native | 多盘 IO 性能更好 |

## 相关笔记
- [[kvm虚拟化部署]] — 具体部署流程
- [[kvm运维]] — 日常运维命令
- [[创建虚拟机]] / [[qcow2虚拟机的创建]]
- [[PVE虚拟化服务器]] — PVE 是 KVM 的封装
- [[Xen和KVM等四大虚拟化架构对比分析]] — 四大架构对比
- [[MOC-Linux运维]]
