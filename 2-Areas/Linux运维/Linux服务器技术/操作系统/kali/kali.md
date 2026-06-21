# Kali Linux

Kali Linux 是 Offensive Security 维护的渗透测试专用 Linux 发行版,基于 Debian Testing 构建,预装 600+ 安全审计与渗透测试工具。

## 系统简介

| 项目 | 说明 |
| --- | --- |
| 维护方 | Offensive Security |
| 上游 | Debian Testing (rolling) |
| 默认桌面 | XFCE(2020.1 起默认) |
| 发布模型 | Kali Rolling(滚动更新) |
| 主要用途 | 渗透测试、安全审计、数字取证 |

## 默认凭据

自 **2020.4** 起,Kali 默认不再以 root 身份登录,改为普通用户:

| 账号 | 默认用户名 | 默认密码 |
| --- | --- | --- |
| 普通用户 | `kali` | `kali` |
| root | (未启用) | (未设置) |

如需直接使用 root,需要先设置 root 密码:

```bash
sudo passwd root
```

## 开启 root 远程登录

新装的 Kali 默认禁止 root 通过 SSH 登录,需要修改 `/etc/ssh/sshd_config`:

```bash
sudo sed -i 's/^#*PermitRootLogin.*/PermitRootLogin yes/' /etc/ssh/sshd_config
grep PermitRootLogin /etc/ssh/sshd_config
sudo systemctl restart ssh
```

关键字段说明:

| 配置项 | 含义 |
| --- | --- |
| `PermitRootLogin yes` | 允许 root 通过 SSH 登录(含密码) |
| `PermitRootLogin prohibit-password` | 仅允许 root 用密钥登录(默认值) |
| `PermitRootLogin no` | 禁止 root 登录 |

> 注意:开放 root SSH 登录会显著降低安全性,测试环境可用,生产环境建议改用密钥登录并禁用密码。

## 分区与文件系统

Kali 安装时常见的分区方案(以 50 GB 磁盘为例):

| 挂载点 | 大小 | 文件系统 | 用途 |
| --- | --- | --- | --- |
| `/boot` | 512 MB | ext4 | 内核与引导 |
| `/` | 20 GB | ext4 | 根文件系统 |
| `/home` | 20 GB | ext4 | 用户数据 |
| `swap` | 内存的 1~2 倍 | swap | 交换分区 |

使用 `lsblk` 与 `df -hT` 查看当前分区布局:

```bash
lsblk
df -hT
```

## 相关笔记

- [[换源]] — 换国内镜像源加速 apt
- [[修改网络配置]] — 静态 IP 与 NetworkManager 配置
- [[配置中文输入法]] — 安装 fcitx5 / ibus 中文输入
- [[MOC-Linux运维]]
