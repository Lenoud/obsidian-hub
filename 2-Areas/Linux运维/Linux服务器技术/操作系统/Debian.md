# Debian

Debian 是最早的 Linux 发行版之一(1993 年发布),完全由社区维护,强调**自由软件**和**稳定性**。Ubuntu / Kali / Raspbian 等都是基于 Debian 的衍生版。

## 版本

| 版本代号 | 版本号 | 状态 | 内核 |
| --- | --- | --- | --- |
| Trixie | 13 | 测试 | 6.x |
| **Bookworm** | **12** | **当前稳定版(2023-2028)** | 6.1 |
| Bullseye | 11 | 老稳定版(2022-2026) | 5.10 |
| Buster | 10 | LTS(2022-2024) | 4.19 |

> Debian 强调稳定,包版本较保守;需要新版本软件建议用 Ubuntu 或 Debian Testing/Backports。

## 与 Ubuntu 的关系

| 维度 | Debian | Ubuntu |
| --- | --- | --- |
| 维护 | Debian Project(社区) | Canonical 公司 |
| 发布节奏 | 约 2 年一个稳定版 | 每 6 个月一个版本 + LTS(2 年) |
| 包数量 | 多 | 多(基于 Debian) |
| 软件版本 | 保守 | 较新 |
| 商业支持 | 无 | Canonical 提供 |
| 默认桌面 | GNOME(可换) | GNOME(可换) |

> Ubuntu 基于 Debian unstable 分支,每 6 个月快照一次。

## 常见问题与解决

### 1. `ls` 目录和文件没颜色

默认 `~/.bashrc` 没启用颜色,手动开启:

```bash
# 临时启用
export LS_OPTIONS='--color=auto'
eval "$(dircolors)"
alias ls='ls $LS_OPTIONS'
alias ll='ls $LS_OPTIONS -l'
alias la='ls $LS_OPTIONS -la'

# 永久启用(写入 ~/.bashrc)
# Debian 默认 ~/.bashrc 有这段代码,只是被注释了,去掉前面的 # 即可
sed -i 's/^# export LS_OPTIONS/export LS_OPTIONS/' ~/.bashrc
sed -i 's/^# eval "$(dircolors)"/eval "$(dircolors)"/' ~/.bashrc
sed -i 's/^# alias ls=/alias ls=/' ~/.bashrc
source ~/.bashrc
```

### 2. 默认没装 sudo(最小安装)

Debian 最小安装默认没 sudo,需要手动加:

```bash
# 切换到 root
su -

# 安装 sudo 并把当前用户加入 sudo 组
apt install -y sudo
usermod -aG sudo <你的用户名>

# 退出重新登录生效
exit
```

### 3. 默认 SSH 不允许 root 登录

```bash
# /etc/ssh/sshd_config
PermitRootLogin yes        # 或 prohibit-password(推荐)

systemctl restart ssh
```

### 4. 中文环境 / 中文输入法

```bash
# 安装中文 locale
apt install -y locales
dpkg-reconfigure locales      # 勾选 zh_CN.UTF-8

# 安装中文输入法(推荐 fcitx5)
apt install -y fcitx5 fcitx5-chinese-addons
im-config -n fcitx5
# 重新登录后生效
```

### 5. 软件源换国内

```bash
# /etc/apt/sources.list(以 Bookworm 为例)
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib non-free non-free-firmware
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib non-free non-free-firmware
deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bookworm-security main contrib non-free non-free-firmware

apt update
```

## 官方资源

- 官网:<https://www.debian.org/>
- 文档:<https://www.debian.org/doc/>
- 镜像列表:<https://www.debian.org/mirror/list>
- 包查询:<https://packages.debian.org/>

## 相关笔记
- [[ubuntu配置源]]
- [[apt基本操作]]
- [[Debian.md]] — 本文
- [[MOC-Linux运维]]
