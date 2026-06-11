# MOC - Linux运维

Linux 服务器运维技术笔记，涵盖操作系统管理、存储配置、网络管理、防火墙、虚拟化、用户权限等核心运维知识。

## 根目录
- [[yum软件源管理]]
- [[网络管理]]
- [[用户和权限管理]]

## Linux服务器技术
- [[crudini命令快速修改ini配置文件]]
- [[nano快捷键]]
- [[vim快捷键]]

### Linux服务器技术/操作系统

- [[AlmaLinux（redhat系列）]]
- [[alpine]]
- [[Debian]]
- [[eve-server]]
- [[Nas网络存储]]
- [[ssh免密]]
- [[SSH配置详解]]
- [[Termux（基于安卓端的一个linux环境）]]
- [[wget介绍]]
- [[终端快捷键]]

#### 操作系统/Centos Stream 9
- [[Centos Stream 9]]
- [[本地源]]

#### 操作系统/centos7
- [[centos查看发行版本]]
- [[crudini&&openstack-config命令]]
- [[dns服务器的配置]]
- [[ftp的安装与配置]]
- [[Linux_cron定时任务]]
- [[Linux-virt-Bridge]]
- [[Linux基础命令]]
- [[netstat查看网络端口命令详细]]
- [[rpm使用]]
- [[vi&vim操作技巧]]
- [[扩容centos-root分区]]
- [[网络操作]]
- [[修改Linux的网卡名称为eth开头]]

##### centos7/yum操作
- [[Yum拉取软件包生成repodata依赖文件]]
- [[Yum命令集合]]
- [[Yum配置优先级]]
- [[Yum软件包组]]
- [[Yum实现不让指定软件升级]]
- [[Yum源下载]]

##### centos7/单用户模式详解
- [[debian系列单用户模式]]
- [[单用户模式详解]]
- [[一次在CentOS系统单用户模式下使用passwd命令破密失败的案例]]

#### 操作系统/kali
- [[kali]]
- [[换源]]
- [[配置中文输入法]]
- [[修改网络配置]]
- [[桌面图标存放目录]]

#### 操作系统/NetworkManager
- [[NetworkManager]]
- [[静态网卡配置]]

#### 操作系统/openEuler
- [[OpenEuler 22.03换源]]
- [[openEuler]]
- [[重启网卡]]

#### 操作系统/rockylinux（redhat系列）
- [[rockylinux（redhat系列）]]
- [[换源]]

#### 操作系统/tools
- [[gedit文本编辑工具]]
- [[neofetch(展示系统信息的工具)]]
- [[tar使用]]
- [[tcping]]
- [[磁盘格式转换]]
- [[哈希校验工具]]
- [[软链接]]

##### tools/jobs
- [[jobs]]
- [[后台运行脚本]]
- [[注册为脚本为systemctl管理]]

##### tools/linux三剑客
- [[awk]]
- [[grep命令数据过滤和筛选]]
- [[linux三剑客]]
- [[sed]]

##### tools/rsync远程同步
- [[rsync从清华源上拉取软件包]]
- [[rsync远程同步]]

##### tools/网络相关命令
- [[curl 命令用法详解]]
- [[ip route命令]]
- [[lsof命令(可以查看指定进程使用了那些文件)]]
- [[netstat命令]]
- [[nmap网络扫描工具]]
- [[route命令的使用]]
- [[观察服务器对外的网络连接情况。]]
- [[连通性检测]]
- [[流量统计]]
- [[网络连接情况]]
- [[网络相关命令]]
- [[域名相关]]
- [[抓包工具]]

#### 操作系统/ubuntu
- [[Ubuntu 系统默认不生成 cron 日志文件]]
- [[ubuntu20.04修改静态IP]]
- [[ubuntu配置ssh链接root]]
- [[ubuntu配置源]]
- [[查找已经安装的软件包]]
- [[配置中文输入法]]
- [[卸载snap]]
- [[xrdp连接黑屏问题]]

##### ubuntu/adb 安卓调试桥安装
- [[adb 安卓调试桥安装]]
- [[安卓群控投屏工具]]
- [[命令]]

##### ubuntu/apt
- [[apt基本操作]]

##### ubuntu/ubuntu静态网络配置
- [[ubuntu静态网络配置]]
- [[命令行连接WIFI]]

#### 操作系统/群晖NAS
- [[PVE黑群晖]]
- [[群晖NAS]]
- [[物理机群晖]]

#### 操作系统/数据恢复
- [[ext2_3_4]]
- [[数据恢复]]

### Linux服务器技术/存储
- [[CIFS配置(samba服务)]]
- [[GlusterFs]]
- [[nfs安装与配置]]
- [[pv_vg_lv创建和运维]]
- [[扩容磁盘]]
- [[软RAID]]

#### 存储/Ceph分布式存储系统
- [[centos7配置ceph]]
- [[Ceph 核心组件]]
- [[ceph运维]]

#### 存储/openfiler
- [[openfiler]]
- [[仪表盘无法操作lv逻辑卷时修改对应的配置文件]]

### Linux服务器技术/防火墙
- [[iptables]]
- [[sport&dport解释]]

### Linux服务器技术/虚拟化
- [[FreeVM国产虚拟化服务器]]
- [[Xen和KVM等四大虚拟化架构对比分析]]

#### 虚拟化/KVM虚拟化
- [[KVM虚拟化]]
- [[kvm虚拟化部署]]
- [[kvm运维]]

##### KVM虚拟化/创建虚拟机
- [[qcow2虚拟机的创建]]
- [[创建虚拟机]]

#### 虚拟化/PVE虚拟化服务器
- [[n100]]
- [[PVE虚拟化服务器]]
- [[搭建]]

#### 虚拟化/VMware虚拟化
- [[ESX兼容性查询]]
- [[VMware vSphere 8.0 正式发布 带神秘代码]]
- [[vmware-workstation下载地址]]
- [[VMware虚拟化]]
- [[wget-esxi安全策略默认不可以从外面wget东西]]

##### VMware虚拟化/ESXI7.0安装教程
- [[ESXI7.0安装教程]]
- [[ESXi常用命令介绍]]
- [[VMware VCSA 7.0 RC集群系统安装]]

#### 虚拟化/XenServer虚拟化
- [[XenServer虚拟化]]

## 相关领域
- [[MOC-自动化运维]] — Ansible 自动化部署、Shell 脚本
- [[MOC-Docker]] — Docker 安装依赖 Linux 基础
