# centos查看发行版本

CentOS/RHEL/Rocky/AlmaLinux 等 Linux 系统查看发行版本号的多种常用方法。

## 方法总览

| 命令 | 适用范围 | 备注 |
| --- | --- | --- |
| `cat /etc/os-release` | systemd 时代所有发行版 | **推荐**，信息最全 |
| `cat /etc/redhat-release` | RHEL/CentOS/Rocky/AlmaLinux | 老方法，仅版本号 |
| `lsb_release -a` | LSB 兼容发行版 | 需安装 `redhat-lsb` |
| `hostnamectl` | systemd 系统 | 同时显示内核、架构 |
| `uname -r` / `uname -a` | 所有 Linux | 主要查**内核**版本 |
| `rpm -q centos-release` | CentOS | RPM 包方式 |
| `rpm -q redhat-release` | RHEL/Rocky/AlmaLinux | RPM 包方式 |

## 1. /etc/os-release（推荐）

所有使用 systemd 的现代发行版（CentOS 7+、Ubuntu、Debian、Rocky、AlmaLinux 等）都会提供该文件，信息标准化、字段清晰。

```bash
cat /etc/os-release
```

CentOS 7 输出示例：

```text
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

Rocky Linux 9 输出示例：

```text
NAME="Rocky Linux"
VERSION="9.3 (Blue Onyx)"
ID="rocky"
ID_LIKE="rhel centos fedora"
VERSION_ID="9"
PLATFORM_ID="platform:el9"
PRETTY_NAME="Rocky Linux 9.3 (Blue Onyx)"
ANSI_COLOR="0;32"
CPE_NAME="cpe:/o:rocky:rocky:9::baseos"
```

只看版本号可加管道过滤：

```bash
grep PRETTY_NAME /etc/os-release
```

## 2. /etc/redhat-release（RHEL 系老方法）

RHEL 系所有分支都会提供该文件，最简单直观：

```bash
cat /etc/redhat-release
```

输出示例：

```text
CentOS Linux release 7.9.2009 (Core)
Rocky Linux release 9.3 (Blue Onyx)
Red Hat Enterprise Linux release 8.8 (Ootpa)
```

## 3. lsb_release

LSB（Linux Standard Base）标准命令，输出规范但需要额外安装 `redhat-lsb` 包。

```bash
lsb_release -a
```

输出示例：

```text
LSB Version:    :core-4.1-amd64:core-4.1-noarch
Distributor ID: CentOS
Description:    CentOS Linux release 7.9.2009 (Core)
Release:        7.9.2009
Codename:       Core
```

> 若提示 `-bash: lsb_release: command not found`，执行 `yum install -y redhat-lsb` 安装。

## 4. hostnamectl

systemd 提供的工具，同时显示操作系统、内核、架构信息，适合一次看清运行环境。

```bash
hostnamectl
```

输出示例：

```text
   Static hostname: centos7-node1
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 7d3c3e6a4f0a4b7a9b3c4d5e6f7a8b9c
           Boot ID: 2b3c4d5e6f7a8b9c0d1e2f3a4b5c6d7e
    Virtualization: kvm
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-1160.el7.x86_64
      Architecture: x86-64
```

## 5. uname（查内核版本）

`uname` 本身不报告发行版，但能查到**正在运行的内核版本**，在排查驱动、模块兼容性时常用。

```bash
uname -a        # 全部信息
uname -r        # 仅内核版本
uname -m        # 机器硬件架构
```

输出示例：

```text
Linux centos7-node1 3.10.0-1160.el7.x86_64 #1 SMP Mon Oct 19 17:59:02 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```

## 6. rpm 包方式

查询提供发行版信息的 RPM 包，适合脚本判断：

```bash
rpm -q centos-release     # CentOS
rpm -q redhat-release     # RHEL/Rocky/AlmaLinux
rpm -qi centos-release    # 显示详细信息
```

输出示例：

```text
centos-release-7-9.2009.1.el7.centos.x86_64
```

## 相关笔记

- [[Linux基础命令]]
- [[MOC-Linux运维]]
