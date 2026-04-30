# openstack 云主机创建命令

openstack 云主机创建命令相关技术笔记。

```bash
controller节点执行
#创建外部网络
openstack network create exnet --enable --project admin --external --description "外部网络" --provider-network-type flat  --provider-physical-network devexnetwork
#创建外部网络子网
#注意业务网卡使用桥接网络这里创建的网段必须与桥接的网络环境一致
openstack subnet create --project admin --dhcp --gateway 192.168.100.1   --ip-version 4 --dns-nameserver 114.114.114.114 --network exnet --subnet-range 192.168.100.0/24 --description "外网子网"  exsubnet
#创建内部网络
openstack network create intnet --enable --project admin --share --internal --description "内部网络"
#创建内网子网
openstack subnet create --project admin –dhcp --dns-nameserver 114.114.114.114 --ip-version 4  --network intnet --subnet-range 172.16.0.0/24 --description  "内网子网"  intsubnet
#创建路由器
openstack router create --enable --project admin --external-gateway exnet --description "Router"  Router1
#添加内网接口到路由器
openstack router add subnet Router1 intsubnet
```

```bash
# controller节点执行
openstack flavor create  --ram 4096 --disk 100 --vcpus 3  --public  --description "Centos9"  Centos7
```

```bash
放通防火墙。
# controller节点执行
openstack security group create jumpserver --description "all ports open"
#放通规则
openstack security group rule create  --ingress  --protocol tcp jumpserver
openstack security group rule create  --egress  --protocol tcp jumpserver
openstack security group rule create  --ingress  --protocol udp jumpserver
openstack security group rule create  --egress  --protocol udp jumpserver
openstack security group rule create  --ingress  --protocol icmp jumpserver
openstack security group rule create  --egress  --protocol icmp jumpserver

```

```bash
#controller节点执行
openstack volume create --size 20 cloud_disk1

```

```bash
#controller节点执行
#上传centos7镜像
openstack image create --container-format bare --disk-format qcow2 --min-disk 5 --min-ram 1024  --file CentOS-7-x86_64-2009.qcow2 Centos7.9.2009
#创建云主机
openstack server create  --network intnet   --security-group jumpserver  --flavor Centos7 --image Centos7.9.2009  jumpserver_server1
#添加硬盘
openstack server add volume jumpserver_server1 cloud_disk1
#添加浮动ip
openstack  floating ip create  --subnet exsubnet  exnet
openstack server add floating ip jumpserver_server1 462f1883-f1c2-43c7-9e4b-ff45639dc5e8
#创建好的云主机

```
