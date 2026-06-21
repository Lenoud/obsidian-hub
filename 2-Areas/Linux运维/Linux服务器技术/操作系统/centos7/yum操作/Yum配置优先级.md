# Yum配置优先级

数据库配置与权限管理。

同时使用本地源与网络源

使用本地源速度更快，而使用网络源版本更新。

可以通过安装yum-plugin-priorities插件同时使用本地源与网络源

```bash
yum install -y yum-plugin-priorities
```

### 开启插件（默认已经启动）
```bash
vim /etc/yum/pluginconf.d/priorities.conf

[main]
enabled = 1  #默认已经启动
```

```bash
[centos]
name=centos7
baseurl=file:///opt/centos
gpgcheck=0
enabled=1
priority=1

[iaas]
name=iaas-repo
baseurl=file:///opt/iaas/iaas-repo
gpgcheck=0
enabled=1
priority=2

[epel]
name=Extra Packages for Enterprise Linux 7 - $basearch
metalink=https://mirrors.fedoraproject.org/metalink?repo=epel-7&arch=$basearch&infra=$infra&content=$contentdir
failovermethod=priority
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
priority=3
```

**priority=1 设置优先级**

在配置文件中，你可以为不同的软件包库设置优先级。优先级数值越小优先级越高（1 为最高，99 为最低），默认值为 99。例如上面示例中本地源 `priority=1` 优先于网络源 `priority=3`。
