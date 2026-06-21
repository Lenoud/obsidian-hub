# 群晖 NAS

群晖(Synology)是主流的商用 NAS(Network Attached Storage)品牌,DSM(DiskStation Manager)是其基于 Linux 的 NAS 操作系统,提供 Web 界面管理存储、共享、容器(Docker/Container Manager)、虚拟机、照片、监控等服务。

## 常见机型分类

| 系列 | 定位 | 典型用途 |
| --- | --- | --- |
| **J 系列** | 入门,ARM CPU | 家庭备份、轻量存储 |
| **Plus 系列**(DS224+ 等) | 主流,Intel Celeron | 家庭/小企业,Docker、虚拟机 |
| **Plus+**(DS923+ 等) | 中高端,AMD Ryzen | 中小企业,VM、SSD 缓存 |
| **XS / SA 系列** | 企业级 | 大规模存储、高可用 |

## DSM 核心功能

| 套件 | 作用 |
| --- | --- |
| **File Station** | 文件管理 Web 界面 |
| **Synology Drive** | 类似 Dropbox 的文件同步,跨平台客户端 |
| **Photos** | 照片管理,替代 Google Photos |
| **Container Manager** | Docker / Kubernetes 管理(DSM 7.2+) |
| **Virtual Machine Manager** | KVM 虚拟机管理 |
| **Synology Photos / Moments** | AI 照片管理 |
| **Surveillance Station** | IP 摄像头监控 |
| **Hyper Backup** | 多目的地备份(本地/远程/云) |
| **Snapshot Replication** | Btrfs 快照,防勒索 |

## SSH 与命令行

DSM 底层是定制 Linux,可启用 SSH:

```bash
# DSM → 控制面板 → 终端机和 SNMP → 启用 SSH 功能
# 默认端口 22,登录用户是 DSM 创建的账号(不是 root)

# 登录后,sudo 切 root
sudo -i

# DSM 系统命令
synoboot --status              # 启动状态
synoservicectl --status nginx  # 查看 nginx 服务
synopkg list                   # 列出已安装套件
synouser --list                # 列出用户

# 存储池/卷
cat /proc/mdstat               # 软件 RAID 状态
lvs                            # LVM 卷
```

## 共享文件夹的访问方式

| 协议 | 端口 | 客户端 |
| --- | --- | --- |
| **SMB / CIFS** | 445 | Windows / macOS / Linux |
| **NFS** | 2049 | Linux / ESXi |
| **AFP** | 548 | macOS(已弃用) |
| **WebDAV** | 5005/5006 | 跨平台,支持 HTTPS |
| **FTP / SFTP** | 21 / 22 | 通用 |

## Docker / Container Manager

DSM 7.2+ Container Manager 直接支持 Docker Compose。旧版本用 Docker 套件。

```yaml
# 在 Container Manager → 项目 → 新建 中粘贴 docker-compose.yml
# 或 SSH 登录后用 docker compose
services:
  nginx:
    image: nginx:alpine
    container_name: nginx-test
    ports:
      - 8080:80
    volumes:
      - /volume1/docker/nginx/html:/usr/share/nginx/html
```

## 常见问题

| 问题 | 原因 | 解决 |
| --- | --- | --- |
| 磁盘损坏提示"不支援的硬盘" | DSM 限制了非官方硬盘 | 修改 Synohwd 脚本或刷 DSM Patched |
| 速度很慢 | SMART 检查 / 索引 / 套件占用 | 控制面板 → 信息中心 → 看负载 |
| 忘记 admin 密码 | — | DSM → 控制面板 → 更新与恢复 → 重置(保留数据) |
| 升级 DSM 后 Docker 套件消失 | DSM 7.2 后改为 Container Manager | 在套件中心重新安装 |

## 相关笔记
- [[物理机群晖]] — 物理机直装 DSM
- [[PVE黑群晖]] — PVE 虚拟机引导黑群晖
- [[Nas网络存储]]
- [[MOC-Linux运维]]
