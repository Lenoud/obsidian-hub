# VMware 虚拟化

VMware(现为 Broadcom 旗下)是企业虚拟化领域的领导者,产品线覆盖桌面级(Workstation/Fusion)、企业级(ESXi/vSphere)、云(VMware Cloud)。

## 产品矩阵

| 产品 | 类型 | 适用场景 |
| --- | --- | --- |
| **VMware Workstation Pro**(Windows/Linux) | Type-2 桌面 | 个人开发、测试,运行在 Windows/Linux 上 |
| **VMware Fusion**(macOS) | Type-2 桌面 | Mac 上运行 Windows/Linux 虚拟机 |
| **VMware ESXi** | Type-1 裸金属 | 企业服务器虚拟化的底层 hypervisor |
| **vCenter Server** | 管理 | 集中管理多台 ESXi,提供 vMotion / HA / DRS |
| **vSphere** | 套件 | ESXi + vCenter 的组合品牌 |
| **VMware Cloud Foundation** | 云 | 全栈软件定义数据中心 |

> 2024 年起 Workstation Pro 与 Fusion Pro 对个人用户免费,商用仍需授权。

## ESXi vs Workstation

| 维度 | ESXi | Workstation |
| --- | --- | --- |
| 类型 | Type-1(裸金属) | Type-2(运行在宿主 OS 上) |
| 安装目标 | 直接装在物理机 | 装在 Windows / Linux / macOS |
| 性能 | 原生,接近物理 | 有宿主 OS 开销 |
| 管理 | Web Client、vCenter | GUI |
| 集群能力 | vMotion / HA / DRS | 不支持 |
| 适用 | 生产环境 | 开发、个人、学习 |

## ESXi 常用命令(SSH)

```bash
# 启用 SSH:DCUI → Troubleshooting Options → Enable SSH

# 查看所有虚拟机
vim-cmd vmsvc/getallvms

# 启停虚拟机
vim-cmd vmsvc/power.on <vmid>
vim-cmd vmsvc/power.off <vmid>

# 查看主机硬件
esxcli hardware cpu list
esxcli hardware memory get

# 网络管理
esxcli network ip interface ipv4 get
esxcli network ip connection list

# 存储管理
esxcli storage vmfs extent list
esxcli storage filesystem list

# 注册/取消注册虚拟机
vim-cmd solo/registervm /vmfs/volumes/datastore1/vm1/vm1.vmx
vim-cmd vmsvc/unregister <vmid>
```

## VMX 配置文件示例(精简)

```text
# /vmfs/volumes/datastore1/vm1/vm1.vmx
config.version = "8"
virtualHW.version = "19"
guestOS = "ubuntu-64"
memsize = "2048"
numvcpus = "2"
scsi0.present = "TRUE"
scsi0.virtualDev = "vmware-scsi"
scsi0:0.present = "TRUE"
scsi0:0.fileName = "vm1.vmdk"
ethernet0.present = "TRUE"
ethernet0.virtualDev = "vmxnet3"
ethernet0.connectionType = "bridged"
```

## 常见问题

| 问题 | 原因 | 解决 |
| --- | --- | --- |
| 启动虚拟机报错"此主机支持 Intel VT-x,但 Intel VT-x 处于禁用状态" | BIOS 未开 VT-x | 进 BIOS 启用 Intel Virtualization Technology |
| 虚拟机无法拖拽文件 | 未安装 VMware Tools | 装客户机内的 open-vm-tools |
| 网络只能 NAT,无法桥接 | 桥接网卡选错 | 虚拟网络编辑器选正确的物理网卡 |
| vCenter Web Client 卡 | Flash 退役 | 切换到 vSphere Client(HTML5,默认端口 443) |
| Workstation 与 Hyper-V 冲突 | Windows 启用了 Hyper-V/WSL2 | 关闭 Hyper-V:`bcdedit /set hypervisorlaunchtype off` |

## 相关笔记
- [[ESX兼容性查询]] — 查硬件是否在 ESXi HCL 列表
- [[vmware-workstation下载地址]]
- [[ESXI7.0安装教程]]
- [[ESXi常用命令介绍]]
- [[Xen和KVM等四大虚拟化架构对比分析]]
- [[MOC-Linux运维]]
