# LEDE / OpenWrt

LEDE(Linux Embedded Development Environment)是 OpenWrt 的一个分支,2018 年已与 OpenWrt 重新合并,现统称 **OpenWrt**。两者都是面向嵌入式设备(主要是路由器)的 Linux 发行版。

## OpenWrt 软件包管理

OpenWrt 用 `opkg` 作为包管理器(类似 apt/yum,但更轻量):

```bash
# 更新包索引(必做)
opkg update

# 安装包
opkg install cfdisk                  # 磁盘分区工具(用于挂载外置硬盘)
opkg install fdisk
opkg install e2fsprogs               # mkfs.ext4
opkg install ntfs-3g                 # NTFS 支持
opkg install luci-app-statistics     # 流量统计 Web 界面

# 卸载
opkg remove <package>

# 列出已安装
opkg list-installed

# 查找
opkg list | grep -i samba
```

## 常用命令

```bash
# 系统信息
cat /etc/openwrt_release
uname -a

# 网络配置(UCI 统一配置接口)
uci show network
uci show wireless

# 重启网络
/etc/init.d/network restart

# 防火墙
uci show firewall
/etc/init.d/firewall restart

# Web 管理界面(LuCI,默认端口 80/443)
/etc/init.d/uhttpd status
/etc/init.d/uhttpd restart
```

## 挂载外置磁盘(常见用例)

OpenWrt 通常装在路由器闪存里(只有 16-128MB),挂载外置 USB 硬盘/U 盘用于扩展存储:

```bash
# 1. 安装依赖
opkg update
opkg install cfdisk e2fsprogs block-mount kmod-fs-ext4 kmod-usb-storage

# 2. 查看磁盘
ls /dev/sd*
# /dev/sda  /dev/sda1

# 3. 分区(cfdisk 交互式)
cfdisk /dev/sda

# 4. 格式化为 ext4
mkfs.ext4 /dev/sda1

# 5. 挂载
mkdir -p /mnt/sda1
mount /dev/sda1 /mnt/sda1

# 6. 开机自动挂载(UCI)
uci add fstab mount
uci set fstab.@mount[-1].device=/dev/sda1
uci set fstab.@mount[-1].target=/mnt/sda1
uci set fstab.@mount[-1].enabled=1
uci set fstab.@mount[-1].fstype=ext4
uci commit fstab
```

## 主流 OpenWrt 发行版

| 发行版 | 特点 |
| --- | --- |
| 官方 OpenWrt | 干净,最小化 |
| **immortalwrt** | OpenWrt 中文优化版,含中国大陆常用包(LuaCore、SSR-Plus 等) |
| LEDE | 已合并回 OpenWrt |
| **openwrt-lean's source** | lean 开源版,含科学上网 |

## 相关笔记
- [[openwrt]] — OpenWrt 实战笔记
- [[RouterOS]] — 另一款软路由系统
- [[immortalwrt]]
- [[MOC-网络技术]]

