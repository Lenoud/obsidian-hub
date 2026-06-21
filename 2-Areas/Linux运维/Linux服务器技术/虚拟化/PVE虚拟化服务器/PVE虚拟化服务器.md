# PVE 虚拟化服务器(Proxmox VE)

Proxmox Virtual Environment(Proxmox VE,简称 PVE)是基于 Debian GNU/Linux 的开源服务器虚拟化管理平台,同时支持 **KVM 虚拟机** 和 **LXC 容器**,提供 Web 界面管理,完全开源(无功能阉割,只有订阅服务差异),是 vsphere / Hyper-V 的主流开源替代。

## 核心特性

| 特性 | 说明 |
| --- | --- |
| **KVM 全虚拟化** | 运行 Windows / Linux / BSD 任何客户机 |
| **LXC 系统容器** | 共享宿主内核,轻量高性能 |
| **集群** | 多节点组成集群,统一管理 |
| **存储** | 支持 LVM / LVM-thin / ZFS / Ceph RBD / NFS / iSCSI |
| **高可用(HA)** | 配合 Ceph / 共享存储,虚拟机自动故障转移 |
| **迁移** | 在线迁移(Live Migration) |
| **备份** | 内置 `vzdump`,支持 dump、LVM snapshot、ZFS snapshot |
| **认证** | 内置 PAM / LDAP / Active Directory |

## 目录与文件路径

```text
/etc/pve/                # 集群配置(自动同步到所有节点)
├── nodes/<node>/        # 节点配置
├── storage.cfg          # 存储配置
├── qemu-server/<vmid>.conf  # KVM 虚拟机配置
├── lxc/<vmid>.conf      # LXC 容器配置
├── user.cfg             # 用户与权限
├── cluster.cfg          # 集群配置
└── datacenter.cfg       # 数据中心配置

/var/lib/vz/
├── images/<vmid>/       # KVM 磁盘(默认 local storage)
├── dump/                # 备份文件
├── template/iso/        # ISO 镜像
└── snippets/            # cloud-init 用户数据

# 虚拟机磁盘典型路径
/var/lib/vz/images/100/vm-100-disk-0.qcow2
# 如果用 LVM-thin,实际存储在 /dev/pve/vm-100-disk-0
# 如果用 ZFS,存储在 zfs-pool/vm-100-disk-0
```

## 常用命令(qm / pct / pvesh)

```bash
# === KVM 虚拟机(qm)===
qm list                       # 列出所有虚拟机
qm start 100                  # 启动 vmid=100 的虚拟机
qm shutdown 100               # 优雅关机
qm stop 100                   # 强制断电
qm config 100                 # 查看配置
qm set 100 --memory 4096 --cores 4   # 修改配置
qm clone 100 101 --name vm-clone --full  # 完整克隆
qm migrate 100 node2 --online # 在线迁移到 node2

# === LXC 容器(pct)===
pct list
pct start 200
pct enter 200                 # 进入容器(类似 docker exec)
pct config 200
pct set 200 --memory 1024

# === 节点/集群 ===
pvecm status                  # 集群状态
pvecm nodes                   # 节点列表
pveperf                       # 性能基准测试

# === 存储 ===
pvesm status                  # 存储状态
pvesm list local-lvm          # 列出某存储下的卷
```

## Web 界面默认信息

- **访问地址**:`https://<IP>:8006/`
- **默认用户**:`root`
- **默认密码**:安装时设置

## 网络(bridge / vlan / bond)

PVE 默认创建 `vmbr0` 桥接到物理网卡:

```text
# /etc/network/interfaces 示例
auto vmbr0
iface vmbr0 inet static
        address 192.168.100.10/24
        gateway 192.168.100.1
        bridge-ports eno1
        bridge-stp off
        bridge-fd 0

# VLAN aware 桥接(一个桥接承载多 VLAN)
auto vmbr0v
iface vmbr0v inet manual
        bridge-ports eno1.10
        bridge-stp off
        bridge-fd 0
        bridge-vlan-aware yes
        bridge-vids 2-4094
```

## 备份与恢复

```bash
# 备份虚拟机
vzdump 100 --storage local --mode snapshot --compress zstd

# 备份文件位置:/var/lib/vz/dump/vzdump-qemu-100-*.vma.zst

# 恢复(从备份)
qmrestore /var/lib/vz/dump/vzdump-qemu-100-2024_01_01.vma.zst 101

# 计划任务:Web → Datacenter → Backup → Add
```

## 订阅与软件源

PVE 开源免费,但默认软件源需要订阅(企业源)。**家庭/个人使用建议切换到 no-subscription 源**:

```bash
# 禁用企业源
mv /etc/apt/sources.list.d/pve-enterprise.list{,.bak}

# 添加免费源
echo "deb http://download.proxmox.com/debian/pve $(. /etc/os-release; echo $VERSION_CODENAME) pve-no-subscription" \
  > /etc/apt/sources.list.d/pve-no-subscription.list

apt update
```

## 常见问题

| 问题 | 原因 | 解决 |
| --- | --- | --- |
| 启动虚拟机报错 "TASK ERROR: KVM virtualisation detected, but not enabled" | BIOS 未开 VT-x | BIOS 启用 Intel VT-x / AMD-V |
| 集群某节点脱机 | corosync 通讯失败 | `systemctl restart corosync pve-cluster` |
| Web 界面登录很慢 | DNS 解析慢或 pveproxy 异常 | 检查 `/etc/hosts` 是否有本机 IP → 主机名映射 |
| 备份失败 "guest-agent is not running" | VM 内未装 qemu-guest-agent | `apt install qemu-guest-agent` 后启用 |
| 升级后内核变更 | apt 升级未更新引导 | `update-grub` 然后 `reboot` |

## 相关笔记
- [[n100]] — N100 等 x86 小主机用 PVE 的实践
- [[KVM虚拟化]] — PVE 底层基于 KVM
- [[Xen和KVM等四大虚拟化架构对比分析]]
- [[MOC-Linux运维]]
