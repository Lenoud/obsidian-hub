# Windows 10 开启 NFS 客户端

Windows 10 专业版/企业版内置 NFS 客户端,可将 Linux 服务器上的 NFS 共享挂载为 Windows 盘符。

> 参考文档:<https://www.yuque.com/u33971054/atri/rlk469nu0xugwwg4>

## 启用 NFS 客户端

### 方式一:GUI

1. 打开 **控制面板** → **程序与功能** → **启用或关闭 Windows 功能**
2. 勾选 **NFS 服务**(包含 NFS 客户端和管理工具)
3. 点确定,等待安装完成
4. **重启**(不重启 mount 命令不可用)

### 方式二:PowerShell(管理员)

```powershell
# 启用 NFS 客户端
Enable-WindowsOptionalFeature -FeatureName ServicesForNFS-ClientOnly -Online -NoRestart
Enable-WindowsOptionalFeature -FeatureName ClientForNFS-Infrastructure -Online -NoRestart
Enable-WindowsOptionalFeature -FeatureName NFS-Administration -Online -NoRestart

# 重启
Restart-Computer
```

## 挂载 NFS 共享

### 命令行

```powershell
# 挂载(注意 NFS 默认不传 UID/GID,中文/权限会有问题)
mount -o anon \\192.168.1.10\data Z:

# 卸载
umount Z:
```

挂载选项:

| 选项 | 作用 |
| --- | --- |
| `-o anon` | 匿名登录(对应 NFS 服务端的 `insecure` + `anonuid`/`anongid`) |
| `-o mtype=hard` | 硬挂载(默认,断网后 IO 挂起) |
| `-o mtype=soft` | 软挂载(断网后 IO 报错,不推荐) |
| `-o lang=zh-CN` | 文件名字符集(解决中文乱码) |

### Linux 服务端配置(关键)

Windows NFS 客户端默认用匿名用户(UID=-2/GID=-2),Linux 端必须允许:

```bash
# /etc/exports
/data  192.168.1.0/24(rw,sync,insecure,all_squash,anonuid=1000,anongid=1000)
#                                ^^^^^^^^                  ^^^^^^^^^^^^^^^^^^^^^^
#                                insecure 允许非特权端口    anonuid 映射到实际用户
```

应用:

```bash
exportfs -rv
```

## 通过注册表设置固定 UID/GID(可选)

匿名挂载可能遇到权限问题,可用注册表固定 UID/GID:

```text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default

新建 DWORD(32 位):
  AnonymousUid  = 1000  (对应 Linux 用户 UID)
  AnonymousGid  = 1000  (对应 Linux 用户 GID)
```

重启 NFS 服务或重启电脑生效。

## 常见问题

| 问题 | 原因 | 解决 |
| --- | --- | --- |
| `mount -o anon` 报 "网络错误 53" | NFS 服务端未开 / 防火墙挡 | Linux 端 `systemctl start nfs`,`firewall-cmd --add-service=nfs` |
| 挂载成功但只读 | Linux 端 export 没 `rw` 或 `all_squash` 映射错 | 修改 `/etc/exports`,加 `rw,anonuid` |
| 文件名中文乱码 | 字符集不一致 | 挂载加 `-o lang=zh-CN`,Linux 端确认 locale |
| 挂载点速度极慢 | 协议版本协商失败 | Linux 端强制 nfsvers=3 或 4 |

## 相关笔记
- [[nfs安装与配置]] — Linux 服务端配置
- [[windows10 nfs server]] — Windows 作 NFS 服务端
- [[MOC-Windows]]
- [[MOC-Linux运维]]
