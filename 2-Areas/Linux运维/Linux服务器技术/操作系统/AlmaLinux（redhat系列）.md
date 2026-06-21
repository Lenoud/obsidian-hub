# AlmaLinux(Red Hat 系列)

AlmaLinux 是由 CloudLinux 公司发起、社区维护的企业级 Linux 发行版,定位为 CentOS 的**1:1 替代品**,完全兼容 RHEL 软件包。2021 年 CentOS 官方转向 CentOS Stream 后,AlmaLinux 与 Rocky Linux 成为最主流的 RHEL 重建版本。

## 与 RHEL / CentOS / Rocky 的关系

| 发行版 | 维护方 | 定位 |
| --- | --- | --- |
| RHEL | Red Hat | 商业版,需要订阅 |
| CentOS(已停) | Red Hat | 原 RHEL 重建版,2021 年停止 |
| CentOS Stream | Red Hat | RHEL 的滚动开发分支,介于 Fedora 与 RHEL 之间 |
| **AlmaLinux** | AlmaLinux OS Foundation(社区) | RHEL 1:1 重建,免费 |
| Rocky Linux | Rocky Enterprise Software Foundation | RHEL 1:1 重建,免费 |

## 版本对应关系

| AlmaLinux | RHEL | 内核 |
| --- | --- | --- |
| 8.x | 8.x | 4.18 |
| 9.x | 9.x | 5.14 |

## 常用操作

```bash
# 查看版本
cat /etc/almalinux-release
# AlmaLinux release 9.3 (Shambling Puma)

# 查看 RHEL 兼容信息(底层就是 RHEL)
cat /etc/redhat-release

# EPEL 仓库(社区扩展包)
dnf install epel-release

# 与 RHEL 一样使用 dnf(不是 yum,虽然 yum 仍是软链)
dnf update
```

## 替换 CentOS 的迁移

AlmaLinux 官方提供 `almalinux-deploy` 脚本,可在原 CentOS 8 上原地升级,无需重装:

```bash
# 在 CentOS 8 上执行
dnf update -y
curl -O https://raw.githubusercontent.com/AlmaLinux/almalinux-deploy/master/almalinux-deploy.sh
chmod +x almalinux-deploy.sh
sudo ./almalinux-deploy.sh
```

## 官方资源

- 官网:<https://almalinux.org/>
- 文档:<https://wiki.almalinux.org/>
- 镜像源列表:<https://mirrors.almalinux.org/>

## 相关笔记
- [[rockylinux（redhat系列）]] — 同为 RHEL 重建,功能等价
- [[centos查看发行版本]] — 查看版本命令在 RHEL 系通用
- [[MOC-Linux运维]]
