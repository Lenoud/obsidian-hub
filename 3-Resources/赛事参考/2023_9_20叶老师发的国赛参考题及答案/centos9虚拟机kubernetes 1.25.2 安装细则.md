双节点配置dns服务114.114.114.114

```text
nmtui  #gui配置好dns
nmcli c reload
nmcli c up ens192
```

下载软件

```ini
#master节点
cat >> /etc/yum.repos.d/local.repo << EOF
[centos]
name=centos
baseurl=file:///opt/centos/BaseOS
gpgcheck=0
enabled=1
EOF
#其他节点  可选 安装vsftp
cat >> /etc/yum.repos.d/local.repo << EOF
[centos]
name=centos
baseurl=ftp://master/centos/BaseOS
gpgcheck=0
enabled=1
EOF

yum -y install rsync* nfs*
```

解压 packages.tar.gz，安装所有本地包

```bash
tar -zxvf packages.tar.gz
yum localinstall -y --skip-broken ./packages/*.rpm
```

关闭防火墙

```text
systemctl stop firewalld
systemctl disable firewalld
setenforce 0
```

安装k8s

```text
kubeeasy install kubernetes   --master 192.168.100.130 \
--worker 192.168.100.131   --user root   --password 000000 \
--version 1.25.2   --offline-file /opt/paas/kubeeasy.tar.gz
```
