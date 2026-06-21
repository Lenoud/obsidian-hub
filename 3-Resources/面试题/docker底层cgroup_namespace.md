# Docker 底层原理:cgroup 与 namespace

Docker 容器的"隔离"和"资源限制"能力不是 Docker 自己实现的,而是依赖 Linux 内核的两大特性:**Namespace**(命名空间,实现隔离)和 **cgroup**(控制组,实现资源限制)。

## Namespace(隔离)

Linux Namespace 让一个进程**看不到**其他 Namespace 里的资源,形成"独立系统"的错觉。

| Namespace | 隔离内容 | Docker 默认启用 |
| --- | --- | --- |
| **PID** | 进程号(容器内看到自己是 PID 1) | ✅ |
| **NET** | 网络栈(网卡、路由表、iptables、端口) | ✅ |
| **IPC** | System V IPC、POSIX 消息队列 | ✅ |
| **MNT** | 文件系统挂载点 | ✅ |
| **UTS** | hostname、domainname | ✅ |
| **USR** | 用户和用户组 ID | ✅(默认不开启,`--userns=host` 关闭) |
| **Cgroup** | cgroup 视图(容器内看到的 cgroup 树) | ✅(Linux 4.6+) |
| **TIME** | 系统时钟(单调时钟/启动时间) | ❌(Linux 5.6+,尚在实验) |

### 查看 Docker 进程的 Namespace

```bash
# 1. 找到容器进程 PID
docker inspect -f '{{.State.Pid}}' <container_name>
# 例:12345

# 2. 查看该进程的所有 namespace
ls -l /proc/12345/ns/
# lrwxrwxrwx ... ipc -> ipc:[4026531839]
# lrwxrwxrwx ... mnt -> mnt:[4026531840]
# lrwxrwxrwx ... net -> net:[4026531956]
# lrwxrwxrwx ... pid -> pid:[4026531836]
# lrwxrwxrwx ... user -> user:[4026531837]
# lrwxrwxrwx ... uts -> uts:[4026531838]

# 3. 用 nsenter 进入容器命名空间
nsenter -t 12345 -m -u -i -n -p bash
```

## cgroup(资源限制)

cgroup(Control Group)对一组进程的**资源用量**做限制和统计。

### cgroup v1 vs v2

| 版本 | 特点 | 启用 |
| --- | --- | --- |
| **v1** | 每种资源(cpu/memory/blkio)独立目录,层级分散 | Linux 2.6.24+ |
| **v2**(推荐) | 统一层级,统一管理,更简洁 | Linux 4.5+,Docker 20.10+ 默认 |

```bash
# 查看 cgroup 版本
stat -fc %T /sys/fs/cgroup/
# cgroup2fs → v2
# tmpfs → v1

# Docker 用 v2 的前提:宿主内核 ≥ 4.15 且 Docker ≥ 20.10
```

### Docker 资源限制参数

```bash
docker run -d \
  --name web \
  --cpus="1.5" \                    # 限制使用 1.5 个 CPU 核心
  --cpu-shares=512 \                # CPU 权重(默认 1024,相对值)
  --memory="1g" \                   # 内存上限 1GB
  --memory-swap="2g" \              # 内存 + swap 上限 2GB
  --memory-reservation="512m" \     # 内存软限(达到时不杀,只回收)
  --pids-limit=200 \                # 最大进程数
  --blkio-weight=500 \              # 块 IO 权重(10-1000)
  --device-read-bps=/dev/sda:10mb \ # /dev/sda 读 10MB/s
  nginx:alpine
```

## 两者的协作关系

```text
┌────────────────────────────────────┐
│  Docker 容器进程                   │
│  ┌──────────────────────────────┐  │
│  │  Namespace(PID/MNT/NET/...) │  │ ← 隔离:看不到外部
│  └──────────────────────────────┘  │
│  ┌──────────────────────────────┐  │
│  │  cgroup(cpu/memory/blkio)   │  │ ← 限制:用不了更多
│  └──────────────────────────────┘  │
└────────────────────────────────────┘
```

Namespace 让容器**自以为独占系统**,cgroup 让容器**用不掉超过额度的资源**。两者结合实现"轻量级虚拟化"。

## 联合文件系统(OverlayFS)

容器镜像分层、写时复制由 **OverlayFS**(或旧版本的 AUFS/DeviceMapper)实现:

```bash
# 查看 OverlayFS 挂载
mount | grep overlay
# overlay on /var/lib/docker/overlay2/.../merged type overlay
#   (lowerdir=只读层,upperdir=读写层,workdir=工作目录)

# 容器修改文件时,从 lower(只读)复制到 upper(读写),不影响其他容器
```

## 常见面试问题

| 问题 | 答案 |
| --- | --- |
| Docker 和虚拟机的区别? | 虚拟机硬件级虚拟化,每个 VM 有完整内核;Docker 操作系统级虚拟化,共享宿主内核,启动快、占用少 |
| Docker 是怎么实现隔离的? | Linux Namespace(PID/MNT/NET/...) |
| Docker 怎么限制 CPU/内存? | Linux cgroup(v1 或 v2) |
| 一个容器最多能用多少内存? | 由 `--memory` 限制,不设置则可吃光宿主机内存(OOM 时被内核 kill) |
| 容器内为什么 PID 是 1? | PID Namespace 隔离,容器内第一个进程就是 PID 1 |

## 相关笔记
- [[MOC-Docker]] — Docker 整体知识
- [[containerd运行时命令]] — 容器运行时
- [[Docker搭建]] — Docker 安装
- [[docker daemon.json配置]] — Docker 守护进程配置
- [[MOC-面试题]]
