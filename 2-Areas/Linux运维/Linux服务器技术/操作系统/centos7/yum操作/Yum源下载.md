# Yum源下载

YUM 软件源的配置与下载方法。

## 获取Yum软件仓库
```bash
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
```

```text
yum install -y epel-release
```

```bash
#下载阿里云源
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
#如果你使用其他发行版，例如 CentOS 6 或 CentOS 8，可以替换 Centos-7.repo 为相应版

#安装epel-release源
yum install -y epel-release

cat  /etc/yum.repos.d/epel.repo
cd /etc/pki/rpm-gpg/
[root@controller rpm-gpg]# ls
RPM-GPG-KEY-CentOS-7  RPM-GPG-KEY-CentOS-Debug-7  RPM-GPG-KEY-CentOS-Testing-7  RPM-GPG-KEY-EPEL-7

#使用指定的源下载vim
yum --enablerepo=epel -y install  vim

```
