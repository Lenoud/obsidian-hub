# 初始化 Ubuntu 22.04 脚本

本文提供一个 Ubuntu 22.04 系统初始化脚本，包括生成 SSH 密钥、配置清华镜像源、安装常用软件、关闭防火墙和设置时区等操作。

```bash
#!/bin/bash

# 生成 SSH 密钥
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa

# 更新源并安装必要的软件
# 备份原始的 apt 源文件
mkdir /etc/apt/sources.list.d/bak -p
mv /etc/apt/sources.list /etc/apt/sources.list.bak

# 使用阿里云镜像源
cat > /etc/apt/sources.list << EOF
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse

# 以下安全更新软件源包含了官方源与镜像站配置，如有需要可自行修改注释切换
deb http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse
# deb-src http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
EOF

# 更新软件包缓存
apt-get update
apt-get install -y vim nano  net-tools

# 关闭防火墙和安全策略
ufw disable
sed -i 's/^GRUB_CMDLINE_LINUX=".*"/GRUB_CMDLINE_LINUX="selinux=0"/g' /etc/default/grub
update-grub

#设置时区
sudo timedatectl set-timezone Asia/Shanghai
timedatectl
sudo rm /etc/localtime
sudo ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
date

#/etc/netplan/00-installer-config.yaml
# network:
#   ethernets:
#     ens33:
#       addresses:
#       - 192.168.100.50/24
#       gateway4: 192.168.100.2
#       nameservers:
#         addresses:
#         - 223.5.5.5
#         search: []
#   version: 2
```

## 说明

| 步骤 | 操作 | 说明 |
| --- | --- | --- |
| 1 | 生成 SSH 密钥 | RSA 密钥，空密码 |
| 2 | 配置镜像源 | 清华大学镜像站 |
| 3 | 安装软件 | vim、nano、net-tools |
| 4 | 关闭防火墙 | ufw disable |
| 5 | 设置时区 | Asia/Shanghai |
