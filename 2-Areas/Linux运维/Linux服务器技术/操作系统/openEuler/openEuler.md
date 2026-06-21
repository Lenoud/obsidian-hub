# openEuler

openEuler 是由华为开源、OpenAtom 开源基金会孵化、社区维护的 Linux 发行版。最初基于 CentOS,现已独立演进,主要面向服务器、云计算、边缘计算、嵌入式场景,支持 x86_64 / ARM64 / RISC-V 等多架构。

## 与其他发行版的关系

| 发行版 | 维护方 | 定位 |
| --- | --- | --- |
| openEuler | OpenAtom 开源基金会(社区) | 多架构服务器/云 |
| EulerOS | 华为(商业版) | 华为云、企业 |
| CentOS Stream | Red Hat | RHEL 上游 |
| openAnolis(龙蜥) | 阿里 | 阿里云生态 |

## 常用命令

```bash
# 查看系统版本
cat /etc/euleros-release         # EulerOS(华为商业版)
cat /etc/openEuler-release       # openEuler(社区版)
# 示例输出:openEuler release 22.03 (LTS-SP3)

# 或用通用命令
cat /etc/os-release

# 查看内核(openEuler 内核通常比 RHEL 新)
uname -r
# 示例:5.10.0-182.0.0.95.oe2203sp3.x86_64
```

## 软件源

openEuler 默认源来自官方,国内访问尚可,可换成清华或阿里:

```bash
# /etc/yum.repos.d/openEuler.repo
# 把 baseurl 改为:
# https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS-SP3/everything/$basearch/
```

```bash
dnf clean all
dnf makecache
dnf update
```

## 与 RHEL/CentOS 的差异

| 维度 | openEuler | CentOS |
| --- | --- | --- |
| 包管理器 | dnf(yum 仍是软链) | yum/dnf |
| 内核 | 5.10+ 较新 | 3.10/4.18 |
| 文件系统 | 默认 ext4,可选 XFS、stratis | 默认 XFS |
| 容器运行时 | 默认 iSulad(华为自研,兼容 OCI) | docker / containerd |
| Web 服务器 | 默认 OpenResty(Nginx+Lua) | httpd / nginx |
| 包格式 | RPM(兼容) | RPM |

## 常见问题

| 问题 | 解决 |
| --- | --- |
| 安装某个软件提示找不到包 | 启用 EPOL(Extended Package set)仓库 |
| Docker 启动失败 | openEuler 默认用 iSulad,装 docker 会冲突;改用 `--containerd=/run/containerd/containerd.sock` 或卸载 iSulad |
| RHEL 软件包能装吗? | 多数 RPM 可兼容,但涉及 glibc/openssl 版本的可能失败 |

## 官方资源

- 官网:<https://www.openeuler.org/>
- 文档:<https://docs.openeuler.org/>
- 镜像:<https://mirrors.tuna.tsinghua.edu.cn/openeuler/>

## 相关笔记
- [[AlmaLinux（redhat系列）]]
- [[rockylinux（redhat系列）]]
- [[centos查看发行版本]]
- [[MOC-Linux运维]]
