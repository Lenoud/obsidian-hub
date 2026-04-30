# Windows 10 NFS Server（haneWIN）

使用 haneWIN 软件在 Windows 10 上搭建 NFS 服务器，供 Linux 客户端挂载使用。

下载地址：https://www.hanewin.net/

## 配置步骤

安装完成后以管理员权限打开软件，编辑 Export 配置：

```
E:\share2 -public -name:nfs
```

关闭 Windows 10 防火墙，然后在 Linux 端挂载：

```bash
mount -t nfs -o nolock 192.168.1.33:/nfs /mnt/share
```

## 参考文档

https://blog.csdn.net/bryanwang_3099/article/details/109900361
