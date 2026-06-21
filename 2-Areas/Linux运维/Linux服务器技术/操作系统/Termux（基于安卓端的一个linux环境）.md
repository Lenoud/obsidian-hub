# Termux(基于安卓端的 Linux 环境)

Termux 是一个安卓平台的终端模拟器,提供完整的 Linux 环境,可通过 `pkg` 包管理器安装 Python / Node / Go / SSH / Git / Vim 等工具,无需 root。适合手机端临时调试、轻量开发、运行脚本。

## 与传统 Linux 的差异

| 维度 | Termux | 传统 Linux |
| --- | --- | --- |
| 文件系统前缀 | `/data/data/com.termux/files/usr`(简称 `$PREFIX`) | `/usr` |
| 用户家目录 | `/data/data/com.termux/files/home`(`$HOME`) | `/root` 或 `/home/user` |
| 包管理器 | `pkg`(基于 apt 的封装) | apt / yum / dnf |
| 权限模型 | 受 Android 沙箱限制,无 root | 完整 Unix 权限 |
| 端口 | < 1024 端口无法监听 | 任意 |
| 内核 | 复用 Android 内核(精简版) | 完整 Linux 内核 |

## 常用命令

```bash
# 更新源
pkg update && pkg upgrade

# 安装常用工具
pkg install python nodejs golang git vim openssh

# 查看已安装
pkg list-installed

# 启动 SSH 服务(可远程连接手机)
sshd
# 默认端口 8022,连接方式:ssh -p 8022 -i ~/.ssh/id_rsa <手机用户名>@<IP>
whoami  # 查看用户名
```

## 访问手机存储

```bash
# 授权访问共享存储(/sdcard)
termux-setup-storage
# 执行后手机会弹窗请求存储权限,允许后会在 $HOME 下生成 storage/ 目录

ls ~/storage/
# dcim  downloads  movies  music  pictures  shared
```

## 安装 Linux 发行版(Proot)

通过 `proot-distro` 可以在 Termux 里安装完整的 Ubuntu / Debian / Alpine:

```bash
pkg install proot-distro
proot-distro list                      # 查看支持的发行版
proot-distro install ubuntu
proot-distro login ubuntu              # 登录 ubuntu 容器
```

## 常见问题

| 问题 | 原因 | 解决 |
| --- | --- | --- |
| `pkg update` 失败 | 默认源国外慢 | 切换清华源(见下) |
| 无法监听 80 端口 | Android 非 root 限制 | 用 8080 等高端口 |
| 软件版本太旧 | Termux 滚动发布 | `pkg upgrade` 升级所有包 |
| 后台被系统杀 | Android 省电策略 | 在系统设置里关闭 Termux 的电池优化,加锁通知 |

## 清华镜像源

```bash
# 替换为清华源
sed -i 's@^\(deb.*stable main\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/apt/termux-main stable main@' $PREFIX/etc/apt/sources.list

pkg update
```

## 官方资源

- GitHub:<https://github.com/termux/termux-app>
- F-Droid(推荐下载渠道,Play Store 版本已停更):<https://f-droid.org/packages/com.termux/>
- Wiki:<https://wiki.termux.com/>

## 相关笔记
- [[Linux基础命令]] — Termux 支持大部分标准 Linux 命令
- [[ubuntu配置源]] — 同样的 apt 换源思路
- [[MOC-Linux运维]]
