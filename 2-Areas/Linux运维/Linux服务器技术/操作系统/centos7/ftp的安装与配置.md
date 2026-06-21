# vsftpd 安装与配置

vsftpd(Very Secure FTP Daemon)是 Linux 上最常用的 FTP 服务器。本文以 CentOS 7 为例,介绍安装、本地用户访问、虚拟用户访问、常见问题。

> FTP 协议本身不加密,公网/生产环境**强烈建议用 SFTP(SSH 自带)或 FTPS(FTP over TLS)**。vsftpd 只在**内网**或**legacy 系统对接**场景下使用。

## 安装

```bash
# 服务端安装(FTP 客户端不需要安装服务)
yum install -y vsftpd ftp

# 启动并设置开机自启
systemctl start vsftpd
systemctl enable vsftpd
```

## 配置文件

主配置文件:`/etc/vsftpd/vsftpd.conf`

### 最小可用配置(本地用户 + 被动模式)

```ini
# /etc/vsftpd/vsftpd.conf

# 允许本地系统用户登录
local_enable=YES
write_enable=YES
local_umask=022

# 用户登录后 chroot 到家目录(限制访问范围)
chroot_local_user=YES
allow_writeable_chroot=YES

# 被动模式端口范围(配合防火墙开放)
pasv_enable=YES
pasv_min_port=30000
pasv_max_port=31000

# 日志
xferlog_enable=YES
xferlog_std_format=YES
```

### 防火墙与 SELinux

```bash
# 防火墙开放端口
firewall-cmd --permanent --add-port=21/tcp
firewall-cmd --permanent --add-port=30000-31000/tcp
firewall-cmd --reload

# SELinux:允许 FTP 读写用户家目录
setsebool -P ftp_home_dir on
setsebool -P ftpd_full_access on
```

### 重启

```bash
systemctl restart vsftpd
systemctl status vsftpd
```

## 用户管理

```bash
# 创建专用 FTP 用户(不允许 SSH 登录)
useradd -d /data/ftp -s /sbin/nologin ftpuser
passwd ftpuser

# 调整家目录权限
mkdir -p /data/ftp
chown ftpuser:ftpuser /data/ftp
chmod 755 /data/ftp
```

## 客户端测试

```bash
# 命令行
ftp <server_ip>
# 用户名:ftpuser
# 密码:***

# 上传 / 下载
ftp> put localfile.txt
ftp> get remotefile.txt
ftp> ls
ftp> bye

# curl(适合脚本)
curl -u ftpuser:password ftp://<server>/file.txt -o local.txt
```

## 常见问题

| 问题 | 原因 | 解决 |
| --- | --- | --- |
| 登录报 `530 Login incorrect` | 用户名密码错 / PAM 限制 | 检查 `/etc/pam.d/vsftpd`,确认用户 shell 在 `/etc/shells` |
| `500 OOPS: vsftpd: refusing to run with writable root inside chroot()` | chroot 根目录可写 | 配置加 `allow_writeable_chroot=YES` |
| 列目录超时,但能登录 | 被动模式端口未开放 | 防火墙开放 pasv_min_port 到 pasv_max_port |
| 上传报 `553 Could not create file` | SELinux / 目录权限 | `setsebool -P ftp_home_dir on`,或 `chmod 755` 目录 |
| 速度很慢 | IPv6 解析 / 反向 DNS | 加 `listen=YES`、`listen_ipv6=NO`、`reverse_lookup_enable=NO` |

## 相关笔记
- [[SSH配置详解]] — 推荐 SFTP 替代 FTP
- [[iptables]] — 防火墙规则
- [[MOC-Linux运维]]
