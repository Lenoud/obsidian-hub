# openstack基本命令

openstack基本命令相关技术笔记。



openstack server list   查看云虚拟机列表
openstack server show cirros  查看cirros的info
openstack image list  查看镜像
openstack network list 查看网络列表
openstack flavor list
openstack-status 显示已安装 OpenStack 服务的状态概览
create   创建命令

1.上传镜像
openstack image create "cirros" --disk-format qcow2  --container-format bare --public   --file ~/images/cirros-0.3.5-x86_64-disk.img  上传镜像
openstack  image set --min-ram 1 --min-disk 2 镜像id/name   更新镜像
–min-ram： 镜像所需的最小 RAM 大小，以兆字节为单位
–min-disk： 镜像所需的最小磁盘大小，以 GB 为单位

2.创建网络
openstack network create  --external --share ext-net （网络名称） 创建外部网络       --provider-network-type  
--provider-physical-network
--provider-segment	 <200>   vlan or vxlan的网段ID
--external： 将此网络设置为外部网络
–share： 在项目之间共享网络
创建外部网络子网
openstack subnet create  --subnet-range 192.168.100.0/24 --gateway 192.168.100.1 --network ext-net  ext-subnet（子网名称）
–subnet-range： 网络地址 192.168.0.0/24
–gateway： 指定子网的网关
–network： 此子网所属的网络（名称或 ID

创建内部网络
openstack network create --share  int-net（网络名称）
创建内部子网
openstack subnet create --subnet-range  10.0.0.1/24  –gateway 10.0.0.1  --network int-net int-subnet（子网名称）

创建名为route 的路由
openstack router create route
openstack router set   --external-gateway ext-net    --fixed-ip   subnet=ext-subnet,ip-address=192.168.200.10  route1（路由名称）  设置路由的属性
–external-gateway： 用作路由器网关的外部网络（名称或 ID）
–fixed-ip： 设置route固定IP地址
语法：subnet=,ip-address=（外部所需的 IP 或子网（名称或 ID）网关）
openstack router add    subnet    route1 （路由名称）    int-net  (网络名称)	添加内网到路由
openstack router list  列出路由列表

3.创建实例类型
openstack flavor create --ram 1024 --disk 3 --vcpus 2 m1.tiny(flavor名称)  创建实列类型

4.创建安全组
openstack  security group list 查看安全组
查看安全组中的安全规则
openstack  security group rule list default(安全组 id/name)
在安全组中添加策略
从入口方向放行所有ICMP、TCP、UDP规则
openstack security group rule create --protocol icmp --ingress  default（安全组 id/name）
openstack security group rule create --protocol tcp --ingress  default（安全组 id/name）
openstack security group rule create --protocol udp --ingress  default（安全组 id/nam）
--ingrees  入站
--egrees   出站

5.创建云主机
openstack server create < --image 镜像名 >  < --flavor flavor规格名> --security-groups 安全组名  < --nic net-id=网络ID >   <云主机name>

命令创建云主机
#第一步：
命令语句 openstack network list
输出结果
注解：在输出结果中，需要记下你所构建的网络的“ID”（编号）。之后你创建虚机时，要用到这个编号。

#第二步：
命令语句 openstack flavor list
输出结果
注解：此命令用于查询你想创建的虚拟机的类型

#第三步：
命令语句 openstack image list
输出结果
注解：选择虚机的镜像文件

#第四步：
命令语句 openstack security group list
输出结果
注解：选择虚机所要使用的安全组的类型。

#第五步：
命令语句 openstack server create --image 镜像名 --flavor flavor规格名 –security-groups 安全组名 --nic net-id=网络ID 虚机名

成功创建的案例  openstack server create --image cirros --flavor  cirros  --nic net-id=630cc94b-5a44-4d56-9c98-e2f9973e48dd --security-group  newdefault  vm-2

输出结果
创建成功后，使用opensatck server list  查看云虚拟机列表

第一步要赋予权限： soutce /etc/keystone/admin-opentc.sh

命令对比：

neutron net-list //查看网络列表，包括内网与外网

neutron subnet-list //查看子网列表，包括内网子网与外网子网

neutron net-show //查看指定网络的详细信息

neutron subnet-show //查看指定子网的详细信息

openstack network list //查看网络列表，包括内网与外网

openstack subnet list//查看子网列表，包括内网子网与外网子网

openstack network show //查看指定网络的详细信息

openstack subnet show //查看指定子网的详细信息

二、创建内网+子网
1、neutron命令创建一个内网

neutron net-create net_name

2、neutron命令创建一个子网

neutron subnet-create int_name 192.168.110.0/24 --name int int_name_sub --gataway 192.168.110.1

删除

neutron net-delete net_name

neutron subnet-delete sub_net_name

查看

neutron net-list

neutron subnet-list

命令自动补全

yum install bash-completion -y

openstack complete | sudo tee /etc/bash_completion.d/osc.bash_completion > /dev/null

echo "source /etc/bash_completion.d/osc.bash_completion" >> ~/.bashrc

三、创建外网
neutron net-create --provider:network_type=vlan --provider:physical_network=provider --provider:segmentation_id=100 --router:external --shared net

type指定类型为vlan

指定物理网络提供者为provider

指定为例网络vlan为100

指定共享 指定创建网络名字

neutron subnet-create net 192.168.110.0/24 --name net_sub --gateway 192.168.110.1

绑定一个网络为net 指定ip段为192.168.110.0/24

指定网关为 192.168.110.1
