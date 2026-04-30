# 初始化centos7脚本

CentOS 7 系统初始化脚本。

```bash
#!/bin/bash

ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa

#yum源初始化
mkdir /etc/yum.repos.d/bak -p
mv /etc/yum.repos.d/C* /etc/yum.repos.d/bak/
curl -o /etc/yum.repos.d/CentOS-Base.repo  https://mirrors.aliyun.com/repo/Centos-7.repo
yum clean all
yum makecache
yum install -y epel-release
yum install -y vim nano chrony net-tools

#关闭防火墙和安全策略
systemctl stop firewalld
systemctl disable firewalld
sed -i s#SELINUX=enforcing#SELINUX=disabled#g /etc/selinux/config

# 备份原始配置文件
cp /etc/chrony.conf /etc/chrony.conf.bak

# 注释掉所有的 pool 行
sed -i 's/^server/#server/g' /etc/chrony.conf

# 配置新的 NTP 服务器
cat >> /etc/chrony.conf << EOF
server ntp1.aliyun.com iburst
server ntp2.aliyun.com iburst
server ntp3.aliyun.com iburst
server ntp4.aliyun.com iburst

EOF

# 重启 Chrony 服务并检查 NTP 源
systemctl restart chronyd

sleep 1s

chronyc makestep
chronyc sources -v
```
