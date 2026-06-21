# Rocky Linux(Red Hat 系列)

Rocky Linux 是由 CentOS 原创始人 Gregory Kurtzer 发起的社区企业级 Linux 发行版,定位为 CentOS 的替代品,与 RHEL 二进制兼容。命名是为纪念另一位 CentOS 联合创始人 Rocky McGaugh。

## 与 AlmaLinux 的差异

| 维度 | Rocky Linux | AlmaLinux |
| --- | --- | --- |
| 发起人 | Gregory Kurtzer(CentOS 创始人) | CloudLinux 公司 |
| 治理 | Rocky Enterprise Software Foundation | AlmaLinux OS Foundation |
| 发布速度 | 通常比 RHEL 晚数天 | 通常比 RHEL 晚数天 |
| 软件包兼容 | 与 RHEL 1:1 | 与 RHEL 1:1 |

两者功能等价,选择依据主要是社区与商业支持偏好。

## 版本对应关系

| Rocky Linux | RHEL | 内核 |
| --- | --- | --- |
| 8.x | 8.x | 4.18 |
| 9.x | 9.x | 5.14 |

## 常用操作

```bash
# 查看版本
cat /etc/rocky-release
# Rocky Linux release 9.3 (Blue Onyx)

# EPEL 仓库
dnf install epel-release

# 启用 CRB(Continuous Release Beta,等价于 CentOS 的 extrasPlus)
dnf config-manager --set-enabled crb
```

## 国内镜像源

```bash
# 替换为阿里云镜像(以 Rocky 9 为例)
sed -e 's|^mirrorlist=|#mirrorlist=|g' \
    -e 's|^#baseurl=http://dl.rockylinux.org/$contentdir|baseurl=https://mirrors.aliyun.com/rockylinux|g' \
    -i.bak \
    /etc/yum.repos.d/rocky*.repo

dnf makecache
```

## 从 CentOS 8 迁移

官方提供 `migrate2rocky` 脚本:

```bash
dnf update -y
curl -O https://raw.githubusercontent.com/rocky-linux/rocky-tools/main/migrate2rocky/migrate2rocky.sh
chmod +x migrate2rocky.sh
sudo ./migrate2rocky.sh -r
```

## 官方资源

- 中文社区官网:<https://www.rockylinux.cn/>
- 官网:<https://rockylinux.org/>
- 文档:<https://docs.rockylinux.org/>

## 相关笔记
- [[AlmaLinux（redhat系列）]] — 同为 RHEL 重建
- [[centos查看发行版本]]
- [[MOC-Linux运维]]
