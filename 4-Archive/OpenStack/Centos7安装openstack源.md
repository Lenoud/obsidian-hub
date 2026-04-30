# Centos7安装openstack源

Centos7安装openstack源相关技术笔记。

启用 OpenStack 存储库
在 CentOS 上，存储库提供 RPM 使 OpenStack repository。CentOS 包含存储库 默认，因此您只需安装软件包即可启用 OpenStack 存储 库。对于 CentOS8，你还需要启用 PowerTools 存储 库。extrasextras

安装 Victoria 版本时，请运行：

```bash
yum install centos-release-openstack-victoria
yum config-manager --set-enabled powertools
```

安装 Ussuri 版本时，请运行：

```bash
yum install centos-release-openstack-ussuri
yum config-manager --set-enabled powertools
```

安装train版本时，请运行：

```bash
yum install centos-release-openstack-train
```

安装 Stein 版本时，请运行：

```bash
yum install centos-release-openstack-stein
```

安装 Rocky 版本时，请运行：

```bash
yum install centos-release-openstack-rocky
```

安装 Queens 版本时，请运行：

```bash
yum install centos-release-openstack-queens
```

安装 Pike 版本时，请运行：

```bash
yum install centos-release-openstack-pike
```

完成安装¶
升级所有节点上的软件包：

```bash
yum upgrade
```

注意

如果升级过程包含新内核，请重新引导主机 以激活它。

为您的版本安装适当的 OpenStack 客户端。

```bash
yum install python-openstackclient
```

RHEL 和 CentOS 默认启用 SELinux。安装软件包以自动管理安全性 OpenStack 服务策略：openstack-selinux

```bash
yum install openstack-selinux
```

#
