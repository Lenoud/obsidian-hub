目录

[一、设计背景](#_Toc148697077)

[1.1项目简介](#_Toc148697078)

[1.2课题目标](#_Toc148697079)

[二、设计思路](#_Toc148697080)

[2.1开发环境与工具](#_Toc148697081)

[2.1.1 VMware Workstation](#_Toc148697082)

[2.1.2 Ubuntu](#_Toc148697083)

[2.1.3 Openstack](#_Toc148697084)

[2.1.4 docker](#_Toc148697085)

[2.1.5 jumpserver](#_Toc148697086)

[2.1.6 其他](#_Toc148697087)

[2.2项目结构](#_Toc148697088)

[2.2.1 openstack架构](#_Toc148697089)

[2.2.2 jumpserver架构](#_Toc148697090)

[三、需求分析](#_Toc148697091)

[3.1 拟定公司部门需求](#_Toc148697092)

[3.2 云主机与设备分配规划拓扑](#_Toc148697093)

[3.3 软件和应用需求](#_Toc148697094)

[3.4 安全性和数据隐私](#_Toc148697095)

[3.5 灾难恢复和业务连续性](#_Toc148697096)

[四、系统设计](#_Toc148697097)

[4.1公司云平台系统架构](#_Toc148697098)

[4.2系统组件](#_Toc148697099)

[4.3系统功能](#_Toc148697100)

[4.4工作流程](#_Toc148697101)

[五、系统实现](#_Toc148697102)

[5.1 openstack部署](#_Toc148697103)

[5.1.1 服务器环境设置](#_Toc148697104)

[5.1.2 网络地址规划](#_Toc148697105)

[5.1.3 配置基础环境](#_Toc148697106)

[5.1.4 配置免密登录](#_Toc148697107)

[5.1.5 时间调整和ntp服务](#_Toc148697108)

[5.1.6 安装openstack客户端](#_Toc148697109)

[5.1.7 安装部署MariaDB](#_Toc148697110)

[5.1.8 安装部署RabbitMQ](#_Toc148697111)

[5.1.9 安装部署Mencache](#_Toc148697112)

[5.1.10 部署配置keystone](#_Toc148697113)

[5.1.11 部署配置glance镜像](#_Toc148697114)

[5.1.12 部署配置placement元数据](#_Toc148697115)

[5.1.13 部署配置nova计算服务](#_Toc148697116)

[5.1.14 配置基于OVS的Neutron网络服务](#_Toc148697117)

[5.1.15 配置dashboard仪表盘服务](#_Toc148697118)

[5.1.16 部署配置cinder卷存储](#_Toc148697119)

[5.2 创建对应资源启动云主机](#_Toc148697120)

[5.2.1 创建网络](#_Toc148697121)

[5.2.2 创建实例类型](#_Toc148697122)

[5.2.3 创建安全组规则](#_Toc148697123)

[5.2.4 创建云硬盘](#_Toc148697124)

[5.2.5 创建云主机](#_Toc148697125)

[5.2.6 测试能否连接云主机](#_Toc148697126)

[5.3 搭建JumpServer管理云平台和云主机](#_Toc148697127)

[5.3.1 配置云主机环境](#_Toc148697128)

[5.3.2 部署jumpserver](#_Toc148697129)

[5.3.3 运维配置jumpserver平台](#_Toc148697130)

[5.3.4 管理云主机](#_Toc148697131)

[六、总结](#_Toc148697132)

[参考文献](#_Toc148697133)

 

# 一、设计背景
## 1.1项目简介
在数字化转型的趋势，各种行业对IT信息投入不断增大，新型的IT架构成为绝大部分行业的支柱，同时企业数据中心底层架构也越来越复杂，相比传统架构，云平台能节省很多成本。本课题主要是为了让更多的用户实现数据与信息管理，提高用户的工作效率，对未来企业发展有着重大的作用。这些新架构不仅提供了更灵活的资源管理和更高的可伸缩性，还为企业数据中心底层架构带来了复杂性挑战。相较于传统架构，云平台的采用可以显著降低成本，提高效率，并允许企业更好地适应市场需求的变化。本课题的关键目标是帮助更多企业用户有效地管理数据与信息，提高工作效率，并为未来企业的可持续发展提供坚实支持。通过云平台的引入，企业能够更好地应对数字化转型挑战，实现更高的创新和竞争力，为迎接未来的商业机遇做好准备。

## 1.2课题目标
基于Opensatck的公司云平台建设与管理方案，通过对云平台的搭建，来实现云平台管理系统对混合云、多云进行管理，简单来说就是管理多个云资源的平台。此课题培养了学生独立学习和社会实践能力，能够加强学生对于Openstack专业的熟练运用和提高学生的综合素质。此项目旨在培养学生在开发和管理基于OpenStack的公司云平台时所需的综合技能，包括云计算概念、架构设计、自动化编排、安全性、成本管理以及多云环境的管理。通过深入研究和实际实施，学生将获得宝贵的独立学习和社会实践经验，不仅提高了他们在OpenStack专业领域的熟练度，还增强了他们的团队协作和解决复杂问题的能力。这个项目强调实际应用和持续学习，有助于学生为未来的职业生涯做好准备。

# 二、设计思路
## 2.1开发环境与工具
本次云平台部署为离线环境，堡垒机部署为联网环境。

表 2-1 使用的工具

| **工具** | **作用** |
| :---: | :---: |
| VMware Workstation | 创建虚拟机 |
| ubuntu | 作为openstack平台系统 |
| Openstack | iaas私有云平台 |
| Centos7 | 云主机镜像 |
| Docker | 部署jumpserver |
| jumpserver | 管理公司云主机和其它服务器 |

### 2.1.1 VMware Workstation
VMware Workstation是一款功能强大的虚拟化软件，由VMware公司开发。它允许用户在单一物理计算机上创建、运行和管理多个虚拟机（虚拟操作系统环境）。

### 2.1.2 Ubuntu
Ubuntu是一个流行的、免费的Linux操作系统，它以易用性和开放源代码而闻名。Ubuntu由Canonical Ltd.维护和支持，提供了广泛的软件包和工具，适用于桌面、服务器和云计算平台。Ubuntu有定期的发布周期，包括LTS（Long Term Support）版本，这些版本获得长期支持和安全更新，以确保稳定性和可靠性。它的社区也非常活跃，支持着大量的用户和开发者，为用户提供了丰富的资源和支持。

### 2.1.3 Openstack
OpenStack是一个强大的开源云计算平台，旨在构建和管理云基础设施。它由一系列相互关联的项目组成，包括计算、网络、存储、身份认证和多个其他组件，每个项目负责不同的云服务功能。OpenStack的目标是提供可扩展性、弹性、可定制性和开放性，使组织能够轻松创建和管理私有云和公有云解决方案。它被广泛用于企业、数据中心和云服务提供商，为用户提供强大的云计算能力。

### 2.1.4 docker
Docker是一种领先的容器化平台，用于打包、交付和运行应用程序及其依赖项。Docker容器是轻量级、可移植的运行环境，包含了应用程序的所有组件，使其在不同的系统上表现一致。Docker容器化方法使开发人员能够将应用程序与其运行时环境隔离开来，提供了一种快速、可靠和可重复的部署方式。它在微服务架构和云原生应用程序开发中非常流行，加速了应用程序交付和扩展的速度。

### 2.1.5 jumpserver
JumpServer 是广受欢迎的开源堡垒机，是符合 4A 规范的专业运维安全审计系统。JumpServer 帮助企业以更安全的方式管控和登录所有类型的资产，实现事前授权、事中监察、事后审计，满足等保合规要求，在此次项目中担任管理公司网络设备的责任。

### 2.1.6 其他
系统版本情况

表 2-2 系统版本

| **系统** | **版本** |
| :---: | :---: |
| Ubuntu | 22.04 |
| Centos7 | 7.9.2009 |
| Openstack | Yoga |
| jumpserver | v3.7.1 |
| Docker | 18.06.3 |

 

## 2.2项目结构
### 2.2.1 openstack架构
OpenStack架构包含多个核心组件，其中包括Cinder、Nova、Glance、Placement、Neutron、Horizon和Keystone。以下是一个简化的OpenStack架构示意图，它包含这些组件以及它们之间的关系：

![[1698991171594-2da1726f-d5fc-4e77-9938-da650107e6c5.png]]

图2-1 openstack架构

### 2.2.2 jumpserver架构
JumpServer 采用分层架构，分别是负载层、接入层、核心层、数据层、存储层。

![[1698991171959-643fbe59-ad3e-4b57-9992-6953735b8f3f.png]]

图2-2  jumpserver架构图

# 三、需求分析
## 3.1 拟定公司部门需求
公司一般包含以下几个部门：运维部、开发部、市场部、客服部、行政部、人力部、财务部、营销部等。

1.运维部：2 台 Linux 服务器用于监控和管理网络设备、1 台 Windows 服务器用于运维工具和应用、网络设备：交换机、路由器、防火墙等。

2.开发部：20 台开发人员工作站，可以考虑使用 Linux 或 Windows，具体取决于开发需求、1 台开发服务器用于版本控制和协作工具、网络设备：交换机、服务器、存储设备等。

3.市场部：10 台员工工作站，可以考虑使用 Windows、网络设备：交换机、打印机、路由器等。

4.客服部：15 台客服代表工作站，可以使用 Windows、网络设备：交换机、电话系统等。

5.行政部：10 台员工工作站，可以使用 Windows、1 台打印服务器用于共享打印机、网络设备：交换机、打印机、路由器等。

6.人力部：5 台员工工作站，可以使用Windows、网络设备：交换机、打印机等。

7.财务部：10 台员工工作站，可以使用 Windows、1 台财务服务器用于财务应用、网络设备：交换机、打印机、存储设备等。

8.营销部：10 台员工工作站，可以使用 Windows、网络设备：交换机、打印机等。

## 3.2 云主机与设备分配规划拓扑
通过上面的需求拟定来分配对应数量的云主机供公司使用。

![[1698991172377-afe0aa20-9779-4f32-bfa5-f008a687bf00.png]]

图3-1 云主机分配规划

## 3.3 软件和应用需求
公司各部门的需求不仅限于硬件资源。还需要详细规划所需的软件和应用程序。例如，开发部可能需要特定的集成开发环境（IDE），财务部需要访问会计软件，市场部需要营销工具等。确保这些需求与硬件资源协调一致，以支持各部门的工作流程。

## 3.4 安全性和数据隐私
随着公司信息的数字化存储和处理，数据安全和隐私保护变得至关重要。设计适当的安全策略和访问控制规则，确保数据不会被未经授权的人员访问。此外，制定应急响应计划以处理潜在的安全威胁。

## 3.5 灾难恢复和业务连续性
考虑制定灾难恢复计划，以确保在不可预测的事件中业务能够继续运行。这包括定期备份关键数据和应用程序，并在需要时能够快速恢复。同时，确保数据中心的物理和网络安全性，以减少潜在风险。

通过综合考虑这些因素，可以制定一个全面的计划，满足公司各部门的需求，同时确保数据和业务的安全性和可用性。

# 四、系统设计
## 4.1公司云平台系统架构
通过搭建openstack云平台后，我们可以使用云平台搭建jumpserver服务器来管理各个部门的云主机和网络设备。

![[1698991172784-d6556b3f-a49a-4fab-8152-900f1f30df85.png]]

图4-1 公司云平台系统架构图

这个系统允许管理员使用JumpServer服务器来管理OpenStack云中的云主机。JumpServer充当管理控制中心，提供身份验证、访问控制、审计和监控功能，以确保云资源的安全和高效管理。

## 4.2系统组件
JumpServer服务器: JumpServer是系统的核心组件，它提供用户身份验证、授权管理和审计功能。管理员可以通过JumpServer访问云主机。

OpenStack云: OpenStack云是云资源的提供者，包括云主机、网络、存储等。JumpServer需要与OpenStack云进行通信来管理和控制云资源。

云主机: OpenStack云中的云主机是需要管理的资源。管理员可以使用JumpServer来远程访问、监控和执行操作，例如创建、删除或调整云主机。

![[1698991173423-8aa1f0b1-097f-435d-9ad0-83217486ced1.png]]

图4-2 系统组件

## 4.3系统功能
系统的功能包括但不限于：用户身份验证：JumpServer负责验证管理员身份，确保只有授权用户能够访问云资源。访问控制：管理员通过JumpServer执行操作，如远程访问、维护和监控云主机。审计和监控：系统记录管理员操作，以审计和监控云资源的使用情况。安全策略：JumpServer实施安全策略，限制管理员对云资源的访问和操作。

高可用性：系统确保JumpServer和OpenStack云的高可用性，以减少中断和故障。防止横向扩展攻击：JumpServer采用最小权限原则，减少横向扩展的风险。

![[1698991173828-158b1bf1-fa88-4836-8c53-44939ea47cad.png]]

图4-3 系统功能

## 4.4工作流程
管理员登录JumpServer服务器并进行身份验证。

管理员使用JumpServer的控制界面选择要管理的云主机。

JumpServer验证管理员的访问权限，然后与OpenStack云进行通信。

JumpServer执行相应的操作，如远程访问云主机、监控性能或执行维护任务。

系统记录管理员的操作，以便审计和监控。

这个系统使管理员能够安全、高效地管理OpenStack云中的云主机，同时确保云资源的安全性和可用性。系统的具体实现将涉及OpenStack和JumpServer的集成、安全策略的制定以及审计和监控功能的实施。

![[1698991174238-bbf38335-737c-46aa-abe2-dae70c92999c.png]]

图4-4 工作流程

# 五、系统实现
## 5.1 openstack部署
### 5.1.1 服务器环境设置
每台服务器的系统环境、角色、物理配置信息。

实验因设备原因无法分配太多物理资源，实际情况应该合理规划服务器配置。

表 5-1 服务器环境

| **角色** | **系统架构** | **内存大小** | **核心数量** |
| :---: | :---: | :---: | :---: |
| Controller | Ubuntu 22.04 | 6G | 4cpu |
| Compute | Ubuntu 22.04 | 9G | 8cpu |

本次服务器全部使用root用户密码为000000

### 5.1.2 网络地址规划
每台服务器的IP地址规划、主机名、信息。

表 5-2 网络规划

| **IP** **地址** | **主机名** | **角色** |
| :---: | :---: | :---: |
| 网卡1：192.168.100.50 nat模式
网卡2: 不配置 桥接模式 | Controller | 控制节点 |
| 网卡1：192.168.100.60 nat模式
网卡2: 不配置 桥接模式 | Compute | 计算节点 |

 

### 5.1.3 配置基础环境
此次openstack云平台为离线安装。

配置基本的环境，配置离线软件源。

#master节点

# 解压

tar zxvf openstackyoga.tar.gz -C /opt/

# 备份文件

cp /etc/apt/sources.list{,.bak}

# 配置离线源

cat > /etc/apt/sources.list << EOF

deb [trusted=yes] file:// /opt/openstackyoga/debs/

EOF

# 清空缓存

apt clean all

# 加载源

apt update

### 5.1.4 配置免密登录
配置节点间的免密登录

#所有节点

echo 192.168.100.50 controller >> /etc/hosts

echo 192.168.100.60 compute >> /etc/hosts

#生成密钥，回显默认全部回车即可

ssh-keygen

ssh-copy-id compute

ssh-copy-id controller

#输入节点密码即可

### 5.1.5 时间调整和ntp服务
配置controller为平台的ntp服务端，同步平台的时间。

#所有节点

# 开启可配置服务

timedatectl set-ntp true

# 调整时区为上海

timedatectl set-timezone Asia/Shanghai

# 将系统时间同步到硬件时间

hwclock –systohc

controller节点执行。

#controller节点

# 安装服务

apt install -y chrony

# 配置文件

vim /etc/chrony/chrony.conf

20 server controller iburst maxsources 2

61 allow all

63 local stratum 10

# 重启服务

systemctl restart chronyd

compute节点执行

# compute节点执行

# 安装服务

apt install -y chrony

# 配置文件

vim /etc/chrony/chrony.conf

20 pool controller iburst maxsources 4

# 重启服务

systemctl restart chronyd

### 5.1.6 安装openstack客户端
安装openstack命令。

#controller节点执行

apt install -y python3-openstackclient

### 5.1.7 安装部署MariaDB
部署数据库服务。

#controller节点执行

apt install -y mariadb-server python3-pymysql

配置mariadb。

#controller节点执行

cat > /etc/mysql/mariadb.conf.d/99-openstack.cnf << EOF

[mysqld]

bind-address = 0.0.0.0

 

default-storage-engine = innodb

innodb_file_per_table = on

max_connections = 4096

collation-server = utf8_general_ci

character-set-server = utf8

EOF

重启根据配置文件启动。

#controller执行

service mysql restart

初始化配置数据库。

#controller执行

mysql_secure_installation

输入数据库密码：回车

可以在没有适当授权的情况下登录到MariaDB root用户，当前已收到保护：n

设置root用户密码：n

删除匿名用户：y

不允许远程root登录：n

删除测试数据库：y

重新加载数据库：y

### 5.1.8 安装部署RabbitMQ
部署消息队列服务器。

#controller节点执行

apt install -y rabbitmq-server #安装服务

#创建openstack用户 #用户名为：openstack #密码：000000

rabbitmqctl add_user openstack 000000

#允许openstack用户进行配置、写入和读取访问

rabbitmqctl set_permissions openstack ".*" ".*" ".*"

### 5.1.9 安装部署Mencache
部署缓存服务。

#controller节点执行

apt install -y memcached python3-memcache

#配置监听地址

vim /etc/memcached.conf

35 -l 0.0.0.0

#重启服务

service memcached restart

### 5.1.10 部署配置keystone
部署keystone认证服务，该组件提供身份认证服务。

#controller节点执行

#创建数据库与用户给予keystone使用

# 创建数据库

CREATE DATABASE keystone;

# 创建用户

GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' IDENTIFIED BY '000000';

#controller节点安装服务

apt install -y keystone

#配置keystone文件

# 备份配置文件

cp /etc/keystone/keystone.conf{,.bak}

# 过滤覆盖文件

grep -Ev "^$|#" /etc/keystone/keystone.conf.bak > /etc/keystone/keystone.conf

 

vim /etc/keystone/keystone.conf

[DEFAULT]

log_dir = /var/log/keystone

[application_credential]

[assignment]

[auth]

[cache]

[catalog]

[cors]

[credential]

[database]

connection = mysql+pymysql://keystone:000000@controller/keystone

[domain_config]

[endpoint_filter]

[endpoint_policy]

[eventlet_server]

[extra_headers]

Distribution = Ubuntu

[federation]

[fernet_receipts]

[fernet_tokens]

[healthcheck]

[identity]

[identity_mapping]

[jwt_tokens]

[ldap]

[memcache]

[oauth1]

[oslo_messaging_amqp]

[oslo_messaging_kafka]

[oslo_messaging_notifications]

[oslo_messaging_rabbit]

[oslo_middleware]

[oslo_policy]

[policy]

[profiler]

[receipt]

[resource]

[revoke]

[role]

[saml]

[security_compliance]

[shadow_users]

[token]

provider = fernet

[tokenless_auth]

[totp]

[trust]

[unified_limit]

[wsgi]

#填充数据库

su -s /bin/sh -c "keystone-manage db_sync" keystone

 

#调用用户和组的密钥库

#这些选项是为了允许在另一个操作系统用户/组下运行密钥库

# 用户

keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone

# 组

keystone-manage credential_setup --keystone-user keystone --keystone-group keystone

#在Queens发布之前，keystone需要在两个单独的端口上运行，以容纳Identity v2 API，后者通常在端口35357上运行单独的仅限管理员的服务。随着v2 API的删除，keystones可以在所有接口的同一端口上运行5000

keystone-manage bootstrap --bootstrap-password 000000 --bootstrap-admin-url http://controller:5000/v3/ --bootstrap-internal-url http://controller:5000/v3/ --bootstrap-public-url http://controller:5000/v3/ --bootstrap-region-id RegionOne

#编辑/etc/apache2/apache2.conf文件并配置ServerName选项以引用控制器节点

echo "ServerName controller" >> /etc/apache2/apache2.conf

#重新启动Apache服务生效配置

service apache2 restart

#配置OpenStack认证环境变量

cat > /etc/keystone/admin-openrc.sh << EOF

export OS_PROJECT_DOMAIN_NAME=Default

export OS_USER_DOMAIN_NAME=Default

export OS_PROJECT_NAME=admin

export OS_USERNAME=admin

export OS_PASSWORD=000000

export OS_AUTH_URL=http://controller:5000/v3

export OS_IDENTITY_API_VERSION=3

export OS_IMAGE_API_VERSION=2

EOF

#加载环境变量

source /etc/keystone/admin-openrc.sh

#创建服务项目，后期组件将使用这个项目

openstack project create --domain default --description "Service Project" service

#验证

openstack token issue

![[1698991174527-62884265-dbbf-4d0e-b3e5-27807f5542ac.png]]

图5-1 检查keystone

### 5.1.11 部署配置glance镜像
该组件提供镜像注册服务，它不直接存储镜像。

#controller节点执行

#创建数据库与用户给予glance使用

# 创建数据库

CREATE DATABASE glance;

# 创建用户

GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' IDENTIFIED BY '000000';

#创建glance浏览用户

openstack user create --domain default --password glance glance

#将管理员角色添加到浏览用户和服务项目

openstack role add --project service --user glance admin

#创建浏览服务实体

openstack service create --name glance --description "OpenStack Image" image

#创建镜像服务API端点

openstack endpoint create --region RegionOne image public http://controller:9292

openstack endpoint create --region RegionOne image internal http://controller:9292

openstack endpoint create --region RegionOne image admin [http://controller:9292](http://controller:9292)

#安装glance镜像服务

apt install -y glance

#配置glance配置文件

# 备份配置文件

cp /etc/glance/glance-api.conf{,.bak}

# 过滤覆盖配置文件

grep -Ev "^$|#" /etc/glance/glance-api.conf.bak > /etc/glance/glance-api.conf

# 配置项信息

vim /etc/glance/glance-api.conf

[DEFAULT]

[barbican]

[barbican_service_user]

[cinder]

[cors]

[database]

connection = mysql+pymysql://glance:000000@controller/glance

[file]

[glance.store.http.store]

[glance.store.rbd.store]

[glance.store.s3.store]

[glance.store.swift.store]

[glance.store.vmware_datastore.store]

[glance_store]

stores = file,http

default_store = file

filesystem_store_datadir = /var/lib/glance/images/

[healthcheck]

[image_format]

disk_formats = ami,ari,aki,vhd,vhdx,vmdk,raw,qcow2,vdi,iso,ploop.root-tar

[key_manager]

[keystone_authtoken]

www_authenticate_uri = http://controller:5000

auth_url = http://controller:5000

memcached_servers = controller:11211

auth_type = password

project_domain_name = Default

user_domain_name = Default

project_name = service

username = glance

password = glance

[oslo_concurrency]

[oslo_messaging_amqp]

[oslo_messaging_kafka]

[oslo_messaging_notifications]

[oslo_messaging_rabbit]

[oslo_middleware]

[oslo_policy]

[oslo_reports]

[paste_deploy]

flavor = keystone

[profiler]

[store_type_location_strategy]

[task]

[taskflow_executor]

[vault]

[wsgi]

#填充数据库

su -s /bin/sh -c "glance-manage db_sync" glance

#重启glance服务生效配置

service glance-api restart

#上传镜像验证

openstack image create --disk-format qcow2 --public --file /home/ubuntu/cirros-0.5.1-x86_64-disk.img cirros

# 查看镜像运行状态

root@controller:~# openstack image list

![[1698991174863-76ef89d4-db7f-417d-ba0f-8666aaf42835.png]]

图5-2 glance部署验证

 

### 5.1.12 部署配置placement元数据
部署placement服务，placement服务跟踪每个供应商的库存和使用情况。例如，在一个计算节点创建一个实例的可消费资源如计算节点的资源提供者的CPU和内存，磁盘从外部共享存储池资源提供商和IP地址从外部IP资源提供者。

#controller节点执行

#创建数据库与用户给予placement使用

# 创建数据库

CREATE DATABASE placement;

# 创建用户

GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'%' IDENTIFIED BY '000000';

#创建服务用户

openstack user create --domain default --password placement placement

#将Placement用户添加到具有管理员角色的服务项目中

openstack role add --project service --user placement admin

#在服务目录中创建Placement API条目

openstack service create --name placement --description "Placement API" placement

#创建Placement API服务端点

openstack endpoint create --region RegionOne placement public http://controller:8778

openstack endpoint create --region RegionOne placement internal http://controller:8778

openstack endpoint create --region RegionOne placement admin http://controller:8778

#安装placement服务

apt install -y placement-api

#配置placement文件

# 备份配置文件

cp /etc/placement/placement.conf{,.bak}

# 过滤覆盖文件

grep -Ev "^$|#" /etc/placement/placement.conf.bak > /etc/placement/placement.conf

# 配置文件

vim /etc/placement/placement.conf

[DEFAULT]

[api]

auth_strategy = keystone

[cors]

[keystone_authtoken]

auth_url = http://controller:5000/v3

memcached_servers = controller:11211

auth_type = password

project_domain_name = Default

user_domain_name = Default

project_name = service

username = placement

password = placement

[oslo_middleware]

[oslo_policy]

[placement]

[placement_database]

connection = mysql+pymysql://placement:000000@controller/placement

[profiler]

#填充数据库

#su -s /bin/sh -c "placement-manage db sync" placement

#重启apache加载placement配置

service apache2 restart

#验证

placement-status upgrade check

![[1698991175145-50a2f068-bbc0-4147-b7cc-98e9ffe5d6b0.png]]

图5-3 placement服务安装配置检查

 

### 5.1.13 部署配置nova计算服务
部署nova服务，Nova是OpenStack中的一个核心组件，它提供了云计算平台中的计算能力管理和虚拟机实例管理功能。

控制节点

控制节点部署nova服务。

#controller节点执行

#创建数据库与用户给予nova使用

# 存放nova交互等数据

CREATE DATABASE nova_api;

# 存放nova资源等数据

CREATE DATABASE nova;

# 存放nova等元数据

CREATE DATABASE nova_cell0;

# 创建管理nova_api库的用户

GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'%' IDENTIFIED BY '000000';

# 创建管理nova库的用户

GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' IDENTIFIED BY '000000';

# 创建管理nova_cell0库的用户

GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'%' IDENTIFIED BY '000000';

#创建nova用户

openstack user create --domain default --password nova nova

#将管理员角色添加到nova用户

openstack role add --project service --user nova admin

#创建nova服务实体

openstack service create --name nova --description "OpenStack Compute" compute

#创建计算API服务端点

openstack endpoint create --region RegionOne compute public http://controller:8774/v2.1

openstack endpoint create --region RegionOne compute internal http://controller:8774/v2.1

openstack endpoint create --region RegionOne compute admin http://controller:8774/v2.1

#安装服务

apt install -y nova-api nova-conductor nova-novncproxy nova-scheduler

#配置nova文件

# 备份配置文件

cp /etc/nova/nova.conf{,.bak}

 

# 过滤提取文件

grep -Ev "^$|#" /etc/nova/nova.conf.bak > /etc/nova/nova.conf

 

# 配置结果

vim /etc/nova/nova.conf

[DEFAULT]

log_dir = /var/log/nova

lock_path = /var/lock/nova

state_path = /var/lib/nova

transport_url = rabbit://openstack:000000@controller:5672/

my_ip = 192.168.100.50

[api]

auth_strategy = keystone

[api_database]

connection = mysql+pymysql://nova:000000@controller/nova_api

[barbican]

[barbican_service_user]

[cache]

[cinder]

[compute]

[conductor]

[console]

[consoleauth]

[cors]

[cyborg]

[database]

connection = mysql+pymysql://nova:000000@controller/nova

[devices]

[ephemeral_storage_encryption]

[filter_scheduler]

[glance]

api_servers = http://controller:9292

[guestfs]

[healthcheck]

[hyperv]

[image_cache]

[ironic]

[key_manager]

[keystone]

[keystone_authtoken]

www_authenticate_uri = http://controller:5000/

auth_url = http://controller:5000/

memcached_servers = controller:11211

auth_type = password

project_domain_name = Default

user_domain_name = Default

project_name = service

username = nova

password = nova

[libvirt]

[metrics]

[mks]

[neutron]

[notifications]

[oslo_concurrency]

lock_path = /var/lib/nova/tmp

[oslo_messaging_amqp]

[oslo_messaging_kafka]

[oslo_messaging_notifications]

[oslo_messaging_rabbit]

[oslo_middleware]

[oslo_policy]

[oslo_reports]

[pci]

[placement]

region_name = RegionOne

project_domain_name = Default

project_name = service

auth_type = password

user_domain_name = Default

auth_url = http://controller:5000/v3

username = placement

password = placement

[powervm]

[privsep]

[profiler]

[quota]

[rdp]

[remote_debug]

[scheduler]

[serial_console]

[service_user]

[spice]

[upgrade_levels]

[vault]

[vendordata_dynamic_auth]

[vmware]

[vnc]

enabled = true

server_listen = $my_ip

server_proxyclient_address = $my_ip

[workarounds]

[wsgi]

[zvm]

[cells]

enable = False

[os_region_name]

openstack =

 

#填充nova_api数据库

su -s /bin/sh -c "nova-manage api_db sync" nova

#注册cell0数据库

su -s /bin/sh -c "nova-manage cell_v2 map_cell0" nova

#创建cell1单元格

su -s /bin/sh -c "nova-manage cell_v2 create_cell --name=cell1 --verbose" nova

#重启相关nova服务加载配置文件

# 处理api服务

service nova-api restart

# 处理资源调度服务

service nova-scheduler restart

# 处理数据库服务

service nova-conductor restart

# 处理vnc远程窗口服务

service nova-novncproxy restart

计算节点

计算节点部署nova服务。

# compute节点执行

#安装nova-compute服务

apt install -y nova-compute

#配置nova文件

# 备份配置文件

cp /etc/nova/nova.conf{,.bak}

 

# 过滤覆盖配置文件

grep -Ev "^$|#" /etc/nova/nova.conf.bak > /etc/nova/nova.conf

# 完整配置

Vim /etc/nova/nova.conf

[DEFAULT]

log_dir = /var/log/nova

lock_path = /var/lock/nova

state_path = /var/lib/nova

transport_url = rabbit://openstack:000000@controller

my_ip = 192.168.100.60

[api]

auth_strategy = keystone

[api_database]

[barbican]

[barbican_service_user]

[cache]

[cinder]

[compute]

[conductor]

[console]

[consoleauth]

[cors]

[cyborg]

[database]

[devices]

[ephemeral_storage_encryption]

[filter_scheduler]

[glance]

api_servers = http://controller:9292

[guestfs]

[healthcheck]

[hyperv]

[image_cache]

[ironic]

[key_manager]

[keystone]

[keystone_authtoken]

www_authenticate_uri = http://controller:5000/

auth_url = http://controller:5000/

memcached_servers = controller:11211

auth_type = password

project_domain_name = Default

user_domain_name = Default

project_name = service

username = nova

password = nova

[libvirt]

[metrics]

[mks]

[neutron]

[notifications]

[oslo_concurrency]

lock_path = /var/lib/nova/tmp

[oslo_messaging_amqp]

[oslo_messaging_kafka]

[oslo_messaging_notifications]

[oslo_messaging_rabbit]

[oslo_middleware]

[oslo_policy]

[oslo_reports]

[pci]

[placement]

region_name = RegionOne

project_domain_name = Default

project_name = service

auth_type = password

user_domain_name = Default

auth_url = http://controller:5000/v3

username = placement

password = placement

[powervm]

[privsep]

[profiler]

[quota]

[rdp]

[remote_debug]

[scheduler]

[serial_console]

[service_user]

[spice]

[upgrade_levels]

[vault]

[vendordata_dynamic_auth]

[vmware]

[vnc]

enabled = true

server_listen = 0.0.0.0

server_proxyclient_address = $my_ip

novncproxy_base_url = [http://192.168.100.50:6080/vnc_auto.html](http://192.168.100.50:6080/vnc_auto.html)

[workarounds]

[wsgi]

[zvm]

[cells]

enable = False

[os_region_name]

openstack =

#检测是否支持硬件加速,如果结果返回0，需要配置如下.

# 确定计算节点是否支持虚拟机的硬件加速

egrep -c '(vmx|svm)' /proc/cpuinfo

# 如果结果返回 “0” ，那么需要配置如下

vim /etc/nova/nova-compute.conf

[libvirt]

virt_type = qemu

#重启服务生效nova配置

service nova-compute restart

控制节点配置主机发现

Nova服务部署完成后，controller节点需要配置发现compute节点。

#查看有那些可用的计算节点

openstack compute service list --service nova-compute

#发现计算主机

su -s /bin/sh -c "nova-manage cell_v2 discover_hosts --verbose" nova

#配置每5分钟主机发现一次

vim /etc/nova/nova.conf

'''

[scheduler]

discover_hosts_in_cells_interval = 300

'''

#重启生效配置

service nova-api restart

#校验nova服务

openstack compute service list

![[1698991175658-5fb58ed6-12b3-40a5-b36d-e261a18f0835.png]]

图5-4 检查nova节点状态

### 5.1.14 配置基于OVS的Neutron网络服务
Neutron是OpenStack的一个网络服务组件，它提供了虚拟网络、子网、端口和路由等功能，帮助用户创建和管理云计算平台中的网络资源。而基于OVS（Open vSwitch）的Neutron网络服务，则是一种在OpenStack中常用的网络架构。

控制节点

Controller节点安装ovs网桥，和neutron服务。

#controller执行

#创建数据库与用给予neutron使用

#创建数据库

CREATE DATABASE neutron;

#创建用户

GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' IDENTIFIED BY '000000';

#创建neutron用户

openstack user create --domain default --password neutron neutron

#向neutron用户添加管理员角色

openstack role add --project service --user neutron admin

#创建neutron实体

openstack service create --name neutron --description "OpenStack Networking" network

#创建neutron的api端点

openstack endpoint create --region RegionOne network public http://controller:9696

openstack endpoint create --region RegionOne network internal http://controller:9696

openstack endpoint create --region RegionOne network admin [http://controller:9696](http://controller:9696)

#配置内核转发

cat >> /etc/sysctl.conf << EOF

# 用于控制系统是否开启对数据包源地址的校验，关闭

net.ipv4.conf.all.rp_filter=0

net.ipv4.conf.default.rp_filter=0

# 开启二层转发设备

net.bridge.bridge-nf-call-iptables=1

net.bridge.bridge-nf-call-ip6tables=1

EOF

#加载模块，作用：桥接流量转发到iptables链

#安装ovs服务

apt install -y neutron-server neutron-plugin-ml2  neutron-l3-agent neutron-dhcp-agent  neutron-metadata-agent neutron-openvswitch-agent

#配置neutron.conf文件 ，用于提供neutron主体服务

# 备份配置文件

cp /etc/neutron/neutron.conf{,.bak}

# 过滤提取配置文件

grep -Ev "^$|#" /etc/neutron/neutron.conf.bak > /etc/neutron/neutron.conf

# 完整配置

vim /etc/neutron/neutron.conf

[DEFAULT]

core_plugin = ml2

service_plugins = router

allow_overlapping_ips = true

auth_strategy = keystone

state_path = /var/lib/neutron

dhcp_agent_notification = true

allow_overlapping_ips = true

notify_nova_on_port_status_changes = true

notify_nova_on_port_data_changes = true

transport_url = rabbit://openstack:000000@controller

[agent]

root_helper = "sudo /usr/bin/neutron-rootwrap /etc/neutron/rootwrap.conf"

[cache]

[cors]

[database]

connection = mysql+pymysql://neutron:000000@controller/neutron

[healthcheck]

[ironic]

[keystone_authtoken]

www_authenticate_uri = http://controller:5000

auth_url = http://controller:5000

memcached_servers = controller:11211

auth_type = password

project_domain_name = default

user_domain_name = default

project_name = service

username = neutron

password = neutron

[nova]

auth_url = http://controller:5000

auth_type = password

project_domain_name = default

user_domain_name = default

region_name = RegionOne

project_name = service

username = nova

password = nova

[oslo_concurrency]

lock_path = /var/lib/neutron/tmp

[oslo_messaging_amqp]

[oslo_messaging_kafka]

[oslo_messaging_notifications]

[oslo_messaging_rabbit]

[oslo_middleware]

[oslo_policy]

[oslo_reports]

[placement]

[privsep]

[quotas]

[ssl]

#配置ml2_conf.ini文件，用户提供二层网络插件服务

# 备份配置文件

cp  /etc/neutron/plugins/ml2/ml2_conf.ini{,.bak}

# 过滤覆盖文件

grep -Ev "^$|#" /etc/neutron/plugins/ml2/ml2_conf.ini.bak > /etc/neutron/plugins/ml2/ml2_conf.ini

# 完整配置

vim /etc/neutron/plugins/ml2/ml2_conf.ini

[DEFAULT]

[ml2]

type_drivers = flat,vlan,vxlan,gre

tenant_network_types = vxlan

mechanism_drivers = openvswitch,l2population

extension_drivers = port_security

[ml2_type_flat]

flat_networks = devexnetwork

[ml2_type_geneve]

[ml2_type_gre]

[ml2_type_vlan]

[ml2_type_vxlan]

vni_ranges = 1:1000

[ovs_driver]

[securitygroup]

enable_ipset = true

enable_security_group = true

firewall_driver = neutron.agent.linux.iptables_firewall.OVSHybridIptablesFirewallDriver

[sriov_driver]

#配置openvswitch_agent.ini文件，提供ovs代理服务

# 备份文件

cp /etc/neutron/plugins/ml2/openvswitch_agent.ini{,.bak}

# 过滤覆盖文件

grep -Ev "^$|#" /etc/neutron/plugins/ml2/openvswitch_agent.ini.bak > /etc/neutron/plugins/ml2/openvswitch_agent.ini

# 完整配置

vim /etc/neutron/plugins/ml2/openvswitch_agent.ini

[DEFAULT]

[agent]

l2_population = True

tunnel_types = vxlan

prevent_arp_spoofing = True

[dhcp]

[network_log]

[ovs]

local_ip = 192.168.100.50

bridge_mappings = devexnetwork:br-ens38

[securitygroup]

#配置l3_agent.ini文件，提供三层网络服务

# 备份文件

cp /etc/neutron/l3_agent.ini{,.bak}

# 过滤覆盖文件

grep -Ev "^$|#" /etc/neutron/l3_agent.ini.bak > /etc/neutron/l3_agent.ini

# 完整配置

vim /etc/neutron/l3_agent.ini

[DEFAULT]

interface_driver = neutron.agent.linux.interface.OVSInterfaceDriver

external_network_bridge =

[agent]

[network_log]

[ovs]

#配置dhcp_agent文件，提供dhcp动态网络服务

# 备份文件

cp /etc/neutron/dhcp_agent.ini{,.bak}

# 过滤覆盖文件

grep -Ev "^$|#" /etc/neutron/dhcp_agent.ini.bak > /etc/neutron/dhcp_agent.ini

# 完整配置

vim /etc/neutron/dhcp_agent.ini

[DEFAULT]

interface_driver = neutron.agent.linux.interface.OVSInterfaceDriver

dhcp_driver = neutron.agent.linux.dhcp.Dnsmasq

enable_isolated_metadata = True

[agent]

[ovs]

#配置metadata_agent.ini文件，提供元数据服务，元数据是什么？ 用来支持如指示存储位置、历史数据、资源查找、文件记录等功能。元数据算是一种电子式目录，为了达到编制目录的目的，必须在描述并收藏数据的内容或特色，进而达成协助数据检索的目的。

# 备份文件

cp  /etc/neutron/metadata_agent.ini{,.bak}

# 过滤覆盖文件

grep -Ev "^$|#" /etc/neutron/metadata_agent.ini.bak > /etc/neutron/metadata_agent.ini

# 完整配置

vim /etc/neutron/metadata_agent.ini

[DEFAULT]

nova_metadata_host = controller

metadata_proxy_shared_secret = ws

[agent]

[cache]

#配置nova文件，主要识别neutron配置，从而能调用网络。

vim /etc/nova/nova.conf

'''

[default]

linuxnet_interface_driver = nova.network.linux_net.LinuxOVSlnterfaceDriver

 

[neutron]

auth_url = http://controller:5000

auth_type = password

project_domain_name = default

user_domain_name = default

region_name = RegionOne

project_name = service

username = neutron

password = neutron

service_metadata_proxy = true

metadata_proxy_shared_secret = ws

 

#填充数据库

su -s /bin/sh -c "neutron-db-manage --config-file /etc/neutron/neutron.conf --config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade head" neutron

#重启nova-api服务生效neutron配置

service nova-api restart

#新建一个外部网络桥接

ovs-vsctl add-br br-eth1

ovs-vsctl add-port br-eth1 eth1

#重启neutron相关服务生效配置

# 提供neutron服务

service neutron-server restart

# 提供ovs服务

service neutron-openvswitch-agent restart

# 提供地址动态服务

service neutron-dhcp-agent restart

# 提供元数据服务

service neutron-metadata-agent restart

# 提供三层网络服务

service neutron-l3-agent restart

# 检查neutron状态

neutron agent-list

![[1698991175933-7e613a83-83e5-4fff-ae58-016fce629a8c.png]]

图5-5 检查neutron状态

计算节点

Compute节点安装ovs和neutron。

#compute执行

#配置内核转发

cat >> /etc/sysctl.conf << EOF

# 用于控制系统是否开启对数据包源地址的校验，关闭

net.ipv4.conf.all.rp_filter=0

net.ipv4.conf.default.rp_filter=0

# 开启二层转发设备

net.bridge.bridge-nf-call-iptables=1

net.bridge.bridge-nf-call-ip6tables=1

EOF

#加载模块，作用：桥接流量转发到iptables链

modprobe br_netfilter

#生效内核配置

sysctl -p

#安装neutron-ovs服务

apt install -y neutron-openvswitch-agent

#配置neutron文件，提供neutron主体服务

# 备份文件

cp /etc/neutron/neutron.conf{,.bak}

# 过滤提取文件

grep -Ev "^$|#" /etc/neutron/neutron.conf.bak > /etc/neutron/neutron.conf

# 完整配置

vim /etc/neutron/neutron.conf

[DEFAULT]

core_plugin = ml2

service_plugins = router

auth_strategy = keystone

state_path = /var/lib/neutron

allow_overlapping_ips = true

transport_url = rabbit://openstack:000000@controller

[agent]

root_helper = "sudo /usr/bin/neutron-rootwrap /etc/neutron/rootwrap.conf"

[cache]

[cors]

[database]

[healthcheck]

[ironic]

[keystone_authtoken]

www_authenticate_uri = http://controller:5000

auth_url = http://controller:5000

memcached_servers = controller:11211

auth_type = password

project_domain_name = default

user_domain_name = default

project_name = service

username = neutron

password = neutron

[nova]

[oslo_concurrency]

lock_path = /var/lib/neutron/tmp

[oslo_messaging_amqp]

[oslo_messaging_kafka]

[oslo_messaging_notifications]

[oslo_messaging_rabbit]

[oslo_middleware]

[oslo_policy]

[oslo_reports]

[placement]

[privsep]

[quotas]

[ssl]

#配置openvswitch_agent.ini文件，提供ovs网络服务

# 备份文件

cp /etc/neutron/plugins/ml2/openvswitch_agent.ini{,.bak}

# 过滤提取文件

grep -Ev "^$|#" /etc/neutron/plugins/ml2/openvswitch_agent.ini.bak > /etc/neutron/plugins/ml2/openvswitch_agent.ini

# 完整配置

vim /etc/neutron/plugins/ml2/openvswitch_agent.ini

#配置nova文件识别neutron配置

vim /etc/nova/nova.conf

'''

[DEFAULT]

linuxnet_interface_driver = nova.network.linux_net.LinuxOVSlnterfaceDriver

vif_plugging_is_fatal = true

vif_pligging_timeout = 300

 

[neutron]

auth_url = http://controller:5000

auth_type = password

project_domain_name = default

user_domain_name = default

region_name = RegionOne

project_name = service

username = neutron

password = neutron

'''

#重启nova服务识别网络配置

service nova-compute restart

#新建一个外部网络桥接

ovs-vsctl add-br br-eth1

#将外部网络桥接映射到网卡

ovs-vsctl add-port br-eth1 eth1

#重启服务加载ovs配置

service neutron-openvswitch-agent restart

校验neutron

检查服务是否启动。

#校验命令

openstack network agent list

![[1698991176274-f4397acd-436a-4e01-8f6e-eeb6e9d207e1.png]]

图5-6 检查服务

### 5.1.15 配置dashboard仪表盘服务
部署openstack的web客户端。

# controller节点执行

#安装服务

apt install -y openstack-dashboard

#配置local_settings.py文件

vim /etc/openstack-dashboard/local_settings.py

'''

# 配置仪表板以在控制器节点上使用OpenStack服务

OPENSTACK_HOST = "controller"

# 在Dashboard configuration部分中，允许主机访问Dashboard

ALLOWED_HOSTS = ["*"]

# 配置memcached会话存储服务

SESSION_ENGINE = 'django.contrib.sessions.backends.cache'

 

CACHES = {

    'default': {

         'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',

         'LOCATION': 'controller:11211',

    }

}

 

# 启用Identity API版本3

OPENSTACK_KEYSTONE_URL = "http://%s:5000/v3" % OPENSTACK_HOST

 

# 启用对域的支持

OPENSTACK_KEYSTONE_MULTIDOMAIN_SUPPORT = True  #添加

# 配置API版本

#添加

OPENSTACK_API_VERSIONS = {

    "identity": 3,

    "image": 2,

    "volume": 3,

}

# 将Default配置为通过仪表板创建的用户的默认域

OPENSTACK_KEYSTONE_DEFAULT_DOMAIN = "Default"  #添加

# 将用户配置为通过仪表板创建的用户的默认角色

OPENSTACK_KEYSTONE_DEFAULT_ROLE = "user"  #添加

# 启用卷备份

#添加

OPENSTACK_CINDER_FEATURES = {

    'enable_backup': True,

}

# 配置时区

TIME_ZONE = "Asia/Shanghai"

'''

#重新加载web服务器配置

systemctl reload apache2

#浏览器访问：[http://192.168.100.50/horizon](http://192.168.100.50/horizon)

。

#登录openstack平台。

域名：Default

用户名：admin

密码：000000

![[1698991176606-f38660d0-2fbc-43d6-9bd6-62e4bc0e2ab8.png]]

图5-7 web界面

![[1698991176912-b25a4de2-f789-474b-926d-1dd21d242c72.png]]

图5-8 登录成功

### 5.1.16 部署配置cinder卷存储
Cinder是OpenStack中的一项项目，它提供了块存储服务，让用户可以在虚拟机实例中使用持久性的块级存储。

控制节点

部署cinder存储服务

#controller节点执行

#创建数据库与用户给予cinder组件使用

# 创建cinder数据库

CREATE DATABASE cinder;

# 创建cinder用户

GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'%' IDENTIFIED BY 'cinderang';

#创建cinder用户

openstack user create --domain default --password cinder cinder

#添加cinder用户到admin角色

openstack role add --project service --user cinder admin

#创建cinder服务实体

#openstack service create --name cinderv3 --description "OpenStack Block Storage" volumev3

#创建cinder服务API端点

openstack endpoint create --region RegionOne volumev3 public http://controller:8776/v3/%\(project_id\)s

openstack endpoint create --region RegionOne volumev3 internal http://controller:8776/v3/%\(project_id\)s

openstack endpoint create --region RegionOne volumev3 admin http://controller:8776/v3/%\(project_id\)s

#安装cinder相关服务

apt install -y cinder-api cinder-scheduler

#配置cinder.conf文件

# 备份文件

cp  /etc/cinder/cinder.conf{,.bak}

# 过滤覆盖文件

grep -Ev "^$|#" /etc/cinder/cinder.conf.bak > /etc/cinder/cinder.conf

# 完整配置

vim /etc/cinder/cinder.conf

[DEFAULT]

transport_url = rabbit://openstack:000000@controller

auth_strategy = keystone

my_ip = 192.168.100.50

[database]

connection = mysql+pymysql://cinder:000000@controller/cinder

[keystone_authtoken]

www_authenticate_uri = http://controller:5000

auth_url = http://controller:5000

memcached_servers = controller:11211

auth_type = password

project_domain_name = default

user_domain_name = default

project_name = service

username = cinder

password = cinder

[oslo_concurrency]

lock_path = /var/lib/cinder/tmp

#填充数据库

su -s /bin/sh -c "cinder-manage db sync" cinder

#配置nova服务可调用cinder服务

vim /etc/nova/nova.conf

[cinder]

os_region_name = RegionOne

'''

#重启nova服务生效cinder服务

service nova-api restart

#重新启动块存储服务

service cinder-scheduler restart

#平滑重启apache服务识别cinder页面

service apache2 reload

计算节点

部署cinder存储服务

#compute节点执行

#安装支持的实用程序包

apt install -y lvm2 thin-provisioning-tools

#创建LVM物理卷

#磁盘根据自己名称指定

pvcreate /dev/sdb

#创建LVM卷组 cinder-volumes

vgcreate cinder-volumes /dev/sdb

#修改lvm.conf文件

#作用：添加接受/dev/sdb设备并拒绝所有其他设备的筛选器

vim /etc/lvm/lvm.conf

devices {

...

filter = [ "a/sdb/", "r/.*/"]

#安装cinder软件包

apt install -y cinder-volume tgt

#配置cinder.conf配置文件

# 备份配置文件

cp /etc/cinder/cinder.conf{,.bak}

# 过滤覆盖文件

grep -Ev "^$|#" /etc/cinder/cinder.conf.bak > /etc/cinder/cinder.conf

# 完整配置文件

vim /etc/cinder/cinder.conf

[DEFAULT]

transport_url = rabbit://openstack:000000@controller

auth_strategy = keystone

my_ip = 192.168.100.60

enabled_backends = lvm

glance_api_servers = http://controller:9292

[database]

connection = mysql+pymysql://cinder:cinderang@controller/cinder

[keystone_authtoken]

www_authenticate_uri = http://controller:5000

auth_url = http://controller:5000

memcached_servers = controller:11211

auth_type = password

project_domain_name = default

user_domain_name = default

project_name = service

username = cinder

password = cinder

[lvm]

volume_driver = cinder.volume.drivers.lvm.LVMVolumeDriver

volume_group = cinder-volumes

target_protocol = iscsi

target_helper = tgtadm

volume_backend_name = lvm

[oslo_concurrency]

lock_path = /var/lib/cinder/tmp

指定卷路径

vim /etc/tgt/conf.d/tgt.conf

include /var/lib/cinder/volumes/*

重新启动块存储卷服务，包括其依赖项

service tgt restart

service cinder-volume restart

验证cinder服务

查看cinder的运行状态。

#controller节点执行

openstack volume service list

至此openstack-yoga部署完成。

## 5.2 创建对应资源启动云主机
### 5.2.1 创建网络
建议云主机给4G以上内存，2个核心以上！命令行创建，第一次执行命令前请导入认证账号，“source /etc/keystone/admin-openrc.sh”

#controller节点执行

#创建外部网络

openstack network create exnet --enable --project admin --external --description "外部网络" --provider-network-type flat  --provider-physical-network devexnetwork

#创建外部网络子网  

#注意业务网卡使用桥接网络这里创建的网段必须与桥接的网络环境一致

openstack subnet create --project admin --dhcp --gateway 192.168.100.1   --ip-version 4 --dns-nameserver 114.114.114.114 --network exnet --subnet-range 192.168.100.0/24 --description "外网子网"  exsubnet

#创建内部网络

openstack network create intnet --enable --project admin --share --internal --description "内部网络"

#创建内网子网

openstack subnet create --project admin –dhcp --dns-nameserver 114.114.114.114 --ip-version 4  --network intnet --subnet-range 172.16.0.0/24 --description  "内网子网"  intsubnet

#创建路由器

openstack router create --enable --project admin --external-gateway exnet --description "Router"  Router1

#添加内网接口到路由器

openstack router add subnet Router1 intsubnet

### 5.2.2 创建实例类型
创建云主机的物理配置类型。

# controller节点执行

openstack flavor create  --ram 4096 --disk 20 --vcpus 2  --public  --description "Centos7"  Centos7

### 5.2.3 创建安全组规则
放通防火墙。

# controller节点执行

openstack security group create jumpserver --description "all ports open"

#放通规则

openstack security group rule create  --ingress  --protocol tcp jumpserver

openstack security group rule create  --egress  --protocol tcp jumpserver

openstack security group rule create  --ingress  --protocol udp jumpserver

openstack security group rule create  --egress  --protocol udp jumpserver

openstack security group rule create  --ingress  --protocol icmp jumpserver

openstack security group rule create  --egress  --protocol icmp jumpserver

### 5.2.4 创建云硬盘
创建一块20G的云硬盘。

#controller节点执行

openstack volume create --size 20 cloud_disk1

### 5.2.5 创建云主机
部署云主机。

#controller节点执行

#上传centos7镜像

openstack image create --container-format bare --disk-format qcow2 --min-disk 5 --min-ram 1024  --file CentOS-7-x86_64-2009.qcow2 Centos7.9.2009

#创建云主机

openstack server create  --network intnet   --security-group jumpserver  --flavor Centos7 --image Centos7.9.2009  jumpserver_server1

#添加硬盘

openstack server add volume jumpserver_server1 cloud_disk1

#添加浮动ip

openstack  floating ip create  --subnet exsubnet  exnet

openstack server add floating ip jumpserver_server1 462f1883-f1c2-43c7-9e4b-ff45639dc5e8

#创建好的云主机

![[1698991177243-7983a1bc-858c-4a0e-9d10-5bd1a9f05233.png]]

图5-9 云主机创建成功

### 5.2.6 测试能否连接云主机
开启Windows10的powershell去ping浮动ip的地址。

![[1698991177557-5ef8558d-3375-48d0-9e40-19f416a01b28.png]]

图5-10 测试连通性

可以看到主机能够与云主机通信，那么我们可以直接使用任意ssh终端连接云主机。

![[1698991177866-3787a149-272c-4dcd-b668-881efaec3385.png]]

图5-11 ssh连接云主机

## 5.3 搭建JumpServer管理云平台和云主机
### 5.3.1 配置云主机环境
需要业务网络能访问互联网，配置软件源，安装docker服务。

#jumpserver_server1云主机执行

#下载docker网络源

rm -rf /etc/yum.repos.d/*

curl -o /etc/yum.repos.d/docker-ce.repo [http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo](http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo)

curl -o /etc/yum.repos.d/CentOS-Base.repo [https://mirrors.aliyun.com/repo/Centos-7.repo](https://mirrors.aliyun.com/repo/Centos-7.repo)

#安装docker

yum -y install docker-ce docker-ce-cli containerd.io

#启动docker

vi /etc/docker/daemon.json #配置镜像加速

{

        "registry-mirrors":["https://fem5eo07.mirror.aliyuncs.com"]

}

systemctl enable docker --now  #开机启动并且立即启动

#关闭防火墙

setenforce 0

sed  -i  s#SELINUX=enforcing#SELINUX=disabled#   /etc/selinux/config

#设置时间

timedatectl set-timezone "Asia/Shanghai"

timedatectl set-time '2023-09-28 11:35:05'

### 5.3.2 部署jumpserver
使用jump官方提供的方式安装jumpserver堡垒机。

# jumpserver_server1云主机执行

#按照官方给出的方式安装

curl -sSL [https://resource.fit2cloud.com/jumpserver/jumpserver/releases/latest/download/quick_start.sh](https://resource.fit2cloud.com/jumpserver/jumpserver/releases/latest/download/quick_start.sh) >> ./install.sh

#执行安装脚本

sh install.sh

#安装时提示是否使用外部服务，全部回车跳过即可

#安装完成

![[1698991178113-3e0205b4-3ec1-4ff7-889b-5bf5cb0133e1.png]]

图5-12 默认回车

![[1698991178413-73ec7595-3ccb-42d2-b51f-c4a88d88ead4.png]]

图5-13 安装成功

### 5.3.3 运维配置jumpserver平台
#### 5.3.3.1 基本用户设置
访问[http://浮动ip:80](http://浮动ip:80)，输入账号admin,密码admin,第一次登录后会提示修改密码。

![[1698991178682-71950fc9-1d1b-4e64-86ea-07a01c6ff7a8.png]]

图5-14 重设密码

重新设置密码后，使用新密码登录平台。

![[1698991178972-961a9a90-e975-4b0f-8ac6-256c03b14ced.png]]

图5-15 登录平台

创建一个运维部账号，单击左侧导航栏，展开“用户管理”项目，选择“用户列表”，单击左侧“创建”按钮。

![[1698991179252-5e499359-6632-4523-8215-e9a69be8e177.png]]

图5-16 创建用户

输入名称：运维部-张三 、用户名：yunwei01、邮箱：[123456@qq.com](mailto:123456@qq.com)，“认证”项，密码策略选择“设置密码”，输入“123456”，取消勾选“下次登录须修改密码”，最后点击提交即可，创建后的默认系统角色为“用户”。

用户：拥有工作台权限

系统管理员：拥有仪表盘、审计台、工作台权限

系统审计员：拥有审计台、工作台权限

![[1698991179668-638ba97d-3a3d-4ab1-a63b-c4b27cb3ae3d.png]]

图5-17 用户“运维部-张三”创建。

#### 5.3.3.2 资产管理
创建好用户后，我们需要分配云主机给用户，此时我们就需要创建资产，下面我们将我们的“jumpserver_server01云主机”分配给“张三”用户，点击“资产管理-->资产列表-->创建-->Linux”。

![[1698991179961-39a8c5ae-0448-46e5-a588-dbb0e9f2a878.png]]

图5-18 创建资产。

名称可以自己定义，这里我们写云主机的名称，ip主机填写“浮动ip”。

![[1698991180279-a0740dd1-7d6c-4c0a-bac3-591058f6528d.png]]

图5-19 创建 jumpserver 资产。

#### 5.3.3.4 账号管理
我们创建完云主机资产后，需要使用一个账号来登录云主机，这里我们直接创建云主机的root账号：“账号：root，密码：000000”。

![[1698991180671-1b05aee7-aaa0-4bf4-b8c3-1330bf73513c.png]]

图5-20 账号添加

![[1698991181072-229febf2-496a-4b31-9e2f-415daf0e6bc3.png]]

图5-21 账号创建

#### 5.3.3.5 权限管理
现在我们将“jumpserver_server01”授权给“运维部-张三”，使其成为张三的资产，张三就可以通过自己的账号密码登录平台，来管理这台云服务器。

![[1698991181458-cbb0481d-78de-4947-bbb7-9d5aec98704c.png]]

图5-22 资产授权

![[1698991181744-1e7f84a0-923d-43f8-9651-b8a89aed2ad9.png]]

图5-23 指定相关资源

 

 

### 5.3.4 管理云主机
登录“运维部-张三”的账号，首次登录需要同意条款，勾选我同意—>提交即可。

![[1698991182109-67c69fc4-7ef6-43d2-87b0-3ee927c9f035.png]]

图5-24 登录

 

点击左上角的个人信息边上的“小按钮”，选中“工作台”，点击我的资产，即可查看到云主机，点击“绿色的commend标志的按钮”，登录云主机可对云主机进行运维操作。

![[1698991182542-b065aaff-6119-4fd4-ba46-841239023ee5.png]]

图5-25 云主机列表。

![[1698991182975-4e7fca85-133f-48df-9b1b-7bf5682e500d.png]]

图5-26云主机登录

![[1698991183490-201e69c4-4e97-4cfa-972c-e53c75c8c44b.png]]

图5-27 云主机运维

同理可以添加公司其它部门的员工账号与服务器进行管理，jumpserver还支持多种设备，和服务管理，如mysql、windows、macos、网络设备“思科、华为，h3c等”、kubernetes、chatgtp等，本项目只举例Linux主机添加。

JumpServer 堡垒机支持的资产类型包括：SSH (Linux / Unix / 网络设备 等)、Windows (Web 方式连接 / 原生 RDP 连接)、数据库 (MySQL / MariaDB / Oracle / SQLServer / PostgreSQL / ClickHouse 等)、NoSQL (Redis / MongoDB 等)、GPT (ChatGPT 等)、云服务 (Kubernetes / VMware vSphere 等)、Web 站点 (各类系统的 Web 管理后台)、应用 (通过 Remote App 连接各类应用)。

至此，项目部署完毕。

六、总结

在这个毕业设计项目中，我成功地搭建了基于OpenStack的公司云平台，并进行了相应的管理方案的设计与实施。首先，我完成了OpenStack云平台的搭建，确保其能够稳定运行。通过这个云平台，我成功地部署了一台JumpServer云主机，这将有助于公司实现更便捷的远程管理和运维操作。在管理方案的实施中，我创建了一个专门的运维部账号，这有助于提高安全性和资源访问的可控性。通过这个账号，运维部门能够有效地管理云平台上的资源，确保其正常运行并满足公司的需求。同时，我将云主机成功地加入了运维部资产，这一步骤进一步简化了资源管理和监控的流程，提高了整体的运维效率。总的来说，通过这个毕业设计项目，我不仅建立了一个稳定的OpenStack云平台，还设计并实施了一套有效的运维管理方案。这将有助于公司更好地管理和利用云平台资源，提高了整体的技术能力和效率。这个项目也为我提供了宝贵的实践经验，增强了我的技术能力和问题解决能力。我要特别感谢我的指导老师，他在整个毕业设计项目中的悉心指导和支持是我成功完成这项任务的关键。老师的专业知识和经验对我产生了深远的影响，帮助我克服了各种困难，并取得了这一成就。再次感谢老师的无私分享和耐心指导，我将终身受益。

# 参考文献
[1]杨慧娟,侯虎,高程.基于openstack的云计算实训平台[J].自动化与仪器仪表,2023(07):161-164+169.DOI:10.14016/j.cnki.1001-9227.2023.07.161.

[2]朱炜龙,徐胜超.开源云平台OpenStack的手动部署过程研究[J].信息与电脑(理论版),2023,35(09):69-71+75.

[3]谢显杰.基于OpenStack的私有云平台构建研究[J].信息与电脑(理论版),2022,34(05):88-91.

[4]李政洋.基于Openstack的高职云计算实践平台构建[J].信息与电脑(理论版),2021,33(17):103-105.

[5]朱梦瑶. 面向OpenStack云平台的网络优化技术研究与实现[D].郑州大学,2021.DOI:10.27466/d.cnki.gzzdu.2021.004787.

[6]尹誉衡.OpenStack Keystone认证机制研究[J].计算机技术与发展,2021,31(02):122-126.

[7]胡振宇,蒋礼青.一种基于Openstack的虚拟机高可用方案[J].电信工程技术与标准化,2021,34(02):60-66.DOI:10.13992/j.cnki.tetas.2021.02.013.

[8]刘国成,吴丹.基于OpenStack的中小企业服务云平台架构研究[J].吉林大学学报(信息科学版),2020,38(06):709-713.DOI:10.19292/j.cnki.jdxxp.2020.06.012.

[9]邹劢豪,董雨鑫,孙建振等.基于Openstack平台的Neutron服务[J].电脑编程技巧与维护,2020(10):12-13+46.DOI:10.16184/j.cnki.comprg.2020.10.004.

[10]蔡洁.基于OpenStack的云计算实训平台建设与应用[J].中阿科技论坛(中英文),2020(10):66-69.

[11]辛子龙,梁栋茂,马睿等.基于OpenStack平台的Glance服务[J].河南科技,2020(23):33-35.

[12]朱园园,董晔.基于OpenStack云资源监控系统设计与实现[J].电脑知识与技术,2019,15(18):241-242.DOI:10.14004/j.cnki.ckt.2019.2025.
