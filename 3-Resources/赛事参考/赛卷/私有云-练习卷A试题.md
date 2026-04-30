_**【赛程名称】云计算赛项第一场-私有云**_

_**某企业拟使用OpenStack搭建一个企业云平台，以实现资源池化弹性管理、企业应用集中管理、统一安全认证和授权等管理。**_

_**系统架构如图1所示，IP地址规划如表1所示。**_

![[1685588988768-a9e90ac1-53a1-4100-8976-8349186a02c4.png]]

_**图1系统架构图**_

_**表1 IP地址规划**_

| _**设备名称**_ | _**主机名**_ | _**接口**_ | _**IP地址**_ | _**说明**_ |
| :---: | :---: | :---: | :---: | :---: |
| _**云服务器1**_ | _**Controller**_ | _**eth0**_ | _**172.129.x.0/24**_ | _**Vlan x**_ |
| | | _**eth1**_ | _**自定义**_ | _**自行创建**_ |
| _**云服务器2**_ | _**Compute**_ | _**eth0**_ | _**172.129.x.0/24**_ | _**Vlan x**_ |
| | | _**eth1**_ | _**自定义**_ | _**自行创建**_ |
| _**云服务器3 ... 云服务器n**_ | _**自定义**_ | _**eth0**_ | _**172.129.x.0/24**_ | _**用于实操题**_ |
| _**PC-1**_ | | _**本地连接**_ | _**172.24.16.0/24**_ | _**PC使用**_ |

_**说明：**_

_**1.竞赛使用集群模式进行，比赛时给每个参赛队提供独立的租户与用户，各用户的资源配额相同，选手通过用户名与密码登录竞赛用私有云平台，创建云主机进行相应答题；**_

_**2.表中的x为工位号；在进行OpenStack搭建时的第二块网卡地址根据题意自行创建；**_

_**3.根据图表给出的信息，检查硬件连线及网络设备配置，确保网络连接正常；**_

_**4.考试所需要的账号资源、竞赛资源包与附件均会在考位信息表与设备确认单中给出；**_

_**5.竞赛过程中，为确保服务器的安全，请自行修改服务器密码；在考试系统提交信息时，请确认自己的IP地址，用户名和密码。**_

_**【任务1】基础运维任务[5.0分]**_

_**【适用平台】私有云**_

_**【题目1】基础环境配置[1.0分]**_

_**使用提供的用户名密码，登录提供的OpenStack私有云平台，在当前租户下，使用CentOS7.9镜像，创建两台云主机，云主机类型使用4vCPU/12G/100G_50G类型。这两台云主机均有一张网卡，自行创建第二张网卡并连接至controller和compute节点（第二张网卡的网段为10.10.X.0/24，X为工位号）。自行检查安全组策略，确保网络正常通信，然后按以下要求配置服务器：**_

_**（1）设置控制节点主机名为controller，设置计算节点主机名为compute；**_

_**（2）修改hosts文件将IP地址映射为主机名；**_

_**完成后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.3分]**_

_**hostnamectl**_

_**【判分标准】[0.3分]**_

_**Static hostname: controller**_

_**【判分结束】**_

_**【检测命令2】[0.4分]**_

_**cat /etc/hosts**_

_**【判分标准】[0.4分]**_

_**controller||compute**_

_**【判分结束】**_

_**【检测命令3】[0.3分]**_

_**ip a**_

_**【判分标准】[0.3分]**_

_**eth1**_

_**【判分结束】**_

_**【题目2】Yum源配置[1.0分]**_

_**使用提供的http服务地址，在http服务下，存在centos7.9和iaas的网络yum源，使用该http源作为安装iaas平台的网络源。分别设置controller节点和compute节点的yum源文件http.repo。完成后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**cat /etc/yum.repos.d/http.repo**_

_**【判分标准】[1.0分]**_

_**iaas-repo**_

_**【判分结束】**_

_**【题目3】计算节点分区[1.0分]**_

_**在compute节点上利用空白分区划分3个10G分区。完成后提交计算节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**lsblk**_

_**【判分标准】[1.0分]**_

_**10G||10G||10G**_

_**【判分结束】**_

_**【题目4】配置无秘钥ssh[1.0分]**_

_**配置controller节点可以无秘钥访问compute节点，配置完成后，尝试ssh连接compute节点的hostname进行测试。完成后提交controller节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**cat .ssh/known_hosts**_

_**【判分标准】[1.0分]**_

_**compute**_

_**【判分结束】**_

_**【题目5】配置主机禁ping[1.0分]**_

_**修改controller节点的/etc/sysctl.conf文件，配置controller节点禁止其他节点可以ping它，配置完之后。完成后提交controller节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**cat /proc/sys/net/ipv4/icmp_echo_ignore_all**_

_**【判分标准】[0.5分]**_

_**1**_

_**【判分结束】**_

_**【检测命令2】[0.5分]**_

_**cat /etc/sysctl.conf**_

_**【判分标准】[0.5分]**_

_**net.ipv4.icmp_echo_ignore_all = 1**_

_**【判分结束】**_

_**【任务2】OpenStack搭建任务[15.0分]**_

_**【适用平台】私有云**_

_**【题目1】基础安装[0.5分]**_

_**在控制节点和计算节点上分别安装openstack-iaas软件包，根据表2配置两个节点脚本文件中的基本变量（配置脚本文件为/etc/openstack/openrc.sh）。**_

_**表2 云平台配置信息**_

| _**服务名称**_ | _**变量**_ | _**参数/密码**_ |
| :---: | :---: | :---: |
| _**Mysql**_ | _**root**_ | _**000000**_ |
| | _**Keystone**_ | _**000000**_ |
| | _**Glance**_ | _**000000**_ |
| | _**Nova**_ | _**000000**_ |
| | _**Neutron**_ | _**000000**_ |
| | _**Heat**_ | _**000000**_ |
| | _**Zun**_ | _**000000**_ |
| _**Keystone**_ | _**DOMAIN_NAME**_ | _**demo**_ |
| | _**Admin**_ | _**000000**_ |
| | _**Rabbit**_ | _**000000**_ |
| | _**Glance**_ | _**000000**_ |
| | _**Nova**_ | _**000000**_ |
| | _**Neutron**_ | _**000000**_ |
| | _**Heat**_ | _**000000**_ |
| | _**Zun**_ | _**000000**_ |
| _**Neutron**_ | _**Metadata**_ | _**000000**_ |
| | _**External Network**_ | _**enp9s0（以实际为准）**_ |

_**完成后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**cat /etc/openstack/openrc.sh |grep -Ev '^$|#'**_

_**【判分标准】[0.5分]**_

_**DOMAIN_NAME=demo**_

_**【判分结束】**_

_**【题目2】数据库安装[1.5分]**_

_**在controller节点上使用iaas-install-mysql.sh 脚本安装Mariadb、Memcached、RabbitMQ等服务。安装服务完毕后，完成下列题目。**_

_**1.修改/etc/my.cnf文件，设置数据库支持大小写。**_

_**2.修改/etc/my.cnf文件，设置数据库缓存innodb表的索引，数据，插入数据时的缓冲为4G。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.5分]**_

_**cat /etc/my.cnf**_

_**【判分标准】[1.5分]**_

_**lower_case_table_names = 1||innodb_buffer_pool_size = 4G**_

_**【判分结束】**_

_**【题目3】Keystone服务安装[1.0分]**_

_**在controller节点上使用iaas-install-keystone.sh脚本安装Keystone服务。安装完成后，使用keystone相关命令，创建用户chinaskill，密码为000000。完成后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.2分]**_

_**mysql -uroot -p000000 -e "show databases;"**_

_**【判分标准】[0.2分]**_

_**keystone**_

_**【判分结束】**_

_**【检测命令2】[0.3分]**_

_**source /etc/keystone/admin-openrc.sh && openstack service list**_

_**【判分标准】[0.3分]**_

_**keystone  | identity**_

_**【判分结束】**_

_**【检测命令3】[0.5分]**_

_**source /etc/keystone/admin-openrc.sh && openstack user list**_

_**【判分标准】[0.5分]**_

_**chinaskill**_

_**【判分结束】**_

_**【题目4】Glance安装[1.0分]**_

_**在controller节点上使用iaas-install-glance.sh脚本安装glance 服务。使用命令将提供的cirros-0.3.4-x86_64-disk.img镜像上传至平台，命名为cirros，并设置最小启动需要的硬盘为10G，最小启动需要的内存为1G。完成后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.2分]**_

_**source /etc/keystone/admin-openrc.sh && openstack-service status|grep glance**_

_**【判分标准】[0.2分]**_

_**Id=openstack-glance-api.service ActiveState=active || Id=openstack-glance-registry.service ActiveState=active**_

_**【判分结束】**_

_**【检测命令2】[0.4分]**_

_**source /etc/keystone/admin-openrc.sh && openstack image show cirros**_

_**【判分标准】[0.4分]**_

_**min_disk         | 10**_

_**【判分结束】**_

_**【检测命令3】[0.4分]**_

_**source /etc/keystone/admin-openrc.sh && openstack image show cirros**_

_**【判分标准】[0.4分]**_

_**min_ram          | 1024**_

_**【判分结束】**_

_**【题目5】Glance服务调优[1.5分]**_

_**glance服务安装完毕后，修改glance响应最大返回项数，该参数默认设置过短，可能导致响应数据被截断，镜像上传失败，修改该参数为1000。完成后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**source /etc/keystone/admin-openrc.sh && openstack-service status|grep glance**_

_**【判分标准】[0.5分]**_

_**Id=openstack-glance-api.service ActiveState=active || Id=openstack-glance-registry.service ActiveState=active**_

_**【判分结束】**_

_**【检测命令2】[0.5分]**_

_**cat /etc/glance/glance-api.conf**_

_**【判分标准】[0.5分]**_

_**api_limit_max = 1000**_

_**【判分结束】**_

_**【检测命令3】[0.5分]**_

_**cat /etc/glance/glance-api.conf**_

_**【判分标准】[0.5分]**_

_**limit_param_default = 1000**_

_**【判分结束】**_

_**【题目6】Nova安装[1.0分]**_

_**在controller节点和compute节点上分别使用iaas-install-placement.sh脚本、iaas-install-nova -controller.sh脚本、iaas-install-nova-compute.sh脚本安装Nova服务。安装完成后，修改相关参数对openstack平台进行调优操作，相应的调优操作有：**_

_**（1）设置cpu超售比例为4倍；**_

_**（2）设置内存超售比例为1.5倍；**_

_**（3）预留2048mb内存，这部分内存不能被虚拟机使用；**_

_**（4）预留10240mb磁盘，这部分磁盘不能被虚拟机使用；**_

_**完成后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.2分]**_

_**source /etc/keystone/admin-openrc.sh && openstack-service status|grep nova**_

_**【判分标准】[0.2分]**_

_**Id=openstack-nova-api.service ActiveState=active**_

_**【判分结束】**_

_**【检测命令2】[0.8分]**_

_**cat /etc/nova/nova.conf|grep -Ev '^$|#'**_

_**【判分标准】[0.8分]**_

_**cpu_allocation_ratio = 4.0||ram_allocation_ratio = 1.5||reserved_host_memory_mb = 2048||reserved_host_disk_mb = 10240**_

_**【判分结束】**_

_**【题目7】修改Nova启动策略[1.5分]**_

_**在平时使用OpenStack平台的时候，当同时启动大量虚拟机时，会出现排队现象，导致虚拟机启动超时从而获取不到IP地址而报错失败，请修改nova相关配置文件，使虚拟机可以在启动完成后获取IP地址，不会因为超时而报错。配置完成后提交controller点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.5分]**_

_**cat /etc/nova/nova.conf**_

_**【判分标准】[1.5分]**_

_**vif_plugging_is_fatal=false**_

_**【判分结束】**_

_**【题目8】Neutron安装[0.5分]**_

_**使用提供的脚本iaas-install-neutron-controller.sh和iaas-install-neutron-compute.sh，在controller和compute节点上安装neutron服务。完成后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.2分]**_

_**source /etc/keystone/admin-openrc.sh && openstack-service status**_

_**【判分标准】[0.2分]**_

_**Id=neutron-linuxbridge-agent.service ActiveState=active**_

_**【判分结束】**_

_**【检测命令2】[0.3分]**_

_**source /etc/keystone/admin-openrc.sh && openstack network agent list**_

_**【判分标准】[0.3分]**_

_**Linux bridge agent | compute    | None              | :-)   | UP**_

_**【判分结束】**_

_**【题目9】Doshboard安装[1.0分]**_

_**在controller节点上使用iaas-install-dashboad.sh脚本安装dashboad服务。安装完成后，将Dashboard中的Djingo数据修改为存储在文件中（此种修改解决了ALL-in-one快照在其他云平台Dashboard不能访问的问题）。完成后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**curl -L http://controller/dashboard**_

_**【判分标准】[0.5分]**_

_**Login - OpenStack Dashboard**_

_**【判分结束】**_

_**【检测命令2】[0.5分]**_

_**cat  /etc/openstack-dashboard/local_settings |grep SESSION**_

_**【判分标准】[0.5分]**_

_**django.contrib.sessions.backends.file**_

_**【判分结束】**_

_**【题目10】Swift安装[1.0分]**_

_**在控制节点和计算节点上分别使用iaas-install-swift-controller.sh和iaas-install-swift-compute.sh脚本安装Swift服务。安装完成后，使用命令创建一个名叫examcontainer的容器，将cirros-0.3.4-x86_64-disk.img镜像上传到examcontainer容器中，并设置分段存放，每一段大小为10M。完成后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.2分]**_

_**source /etc/keystone/admin-openrc.sh && openstack-service status | grep swift**_

_**【判分标准】[0.2分]**_

_**Id=openstack-swift-proxy.service ActiveState=active**_

_**【判分结束】**_

_**【检测命令2】[0.3分]**_

_**source /etc/keystone/admin-openrc.sh && swift stat**_

_**【判分标准】[0.3分]**_

_**Accept-Ranges: bytes**_

_**【判分结束】**_

_**【检测命令3】[0.5分]**_

_**source /etc/keystone/admin-openrc.sh && swift list examcontainer_segments**_

_**【判分标准】[0.5分]**_

_**00000000||00000001**_

_**【判分结束】**_

_**【题目11】Cinder创建硬盘[1.0分]**_

_**在控制节点和计算节点分别使用iaas-install-cinder-controller.sh、iaas-install-cinder-compute.sh脚本安装Cinder服务，请在计算节点，对块存储进行扩容操作，即在计算节点再分出一个5G的分区，加入到cinder块存储的后端存储中去。完成后提交计算节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**vgdisplay**_

_**【判分标准】[1.0分]**_

_**Metadata Areas        2**_

_**【判分结束】**_

_**【题目12】Heat安装[0.5分]**_

_**在控制节点上使用iaas-install-heat.sh脚本安装Heat服务。完成后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**source /etc/keystone/admin-openrc.sh && openstack-service status | grep heat**_

_**【判分标准】[0.5分]**_

_**Id=openstack-heat-api-cfn.service ActiveState=active**_

_**【判分结束】**_

_**【题目13】manila安装[1.5分]**_

_**在控制和计算节点上分别使用iaas-install-manila-controller.sh和iaas-install-manila-compute.sh脚本安装manila服务。安装服务后创建default_share_type共享类型（不使用驱动程序支持），接着创建一个大小为2G的共享存储名为share01。最后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.5分]**_

_**source /etc/keystone/admin-openrc.sh && manila list**_

_**【判分标准】[1.5分]**_

_**share01 | 2    | NFS         | available**_

_**【判分结束】**_

_**【题目14】OpenStack平台内存优化[1.5分]**_

_**搭建完OpenStack平台后，关闭系统的内存共享，打开透明大页。完成后提交控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.5分]**_

_**cat /sys/kernel/mm/transparent_hugepage/defrag**_

_**【判分标准】[1.5分]**_

_**[never]**_

_**【判分结束】**_

_**【任务3】OpenStack运维任务[15.0分]**_

_**【适用平台】私有云**_

_**【题目1】开放镜像[1.0分]**_

_**使用自己搭建的OpenStack私有云平台，在OpenStack平台的admin项目中使用cirros-0.3.4-x86_64-disk.img镜像文件创建名为glance-cirros的镜像，通过OpenStack命令将glance-cirros镜像指定demo项目进行共享使用。配置完成后提交controller点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**source /etc/keystone/admin-openrc.sh && glance member-list --image-id $(openstack image list | grep -w glance-cirros | awk -F '|' '{print $2}')**_

_**【判分标准】[1.0分]**_

_**accepted**_

_**【判分结束】**_

_**【题目2】OpenStack参数调优[1.0分]**_

_**OpenStack各服务内部通信都是通过RPC来交互，各agent都需要去连接RabbitMQ；随着各服务agent增多，MQ的连接数会随之增多，最终可能会到达上限，成为瓶颈。使用自行搭建的OpenStack私有云平台，分别通过用户级别、系统级别、配置文件来设置RabbitMQ服务的最大连接数为10240，配置完成后提交修改节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.2分]**_

_**cat /etc/security/limits.conf**_

_**【判分标准】[0.2分]**_

_**openstack        soft    nofile          10240||openstack        hard    nofile          10240**_

_**【判分结束】**_

_**【检测命令2】[0.3分]**_

_**cat /etc/sysctl.conf**_

_**【判分标准】[0.3分]**_

_**fs.file-max=10240**_

_**【判分结束】**_

_**【检测命令3】[0.3分]**_

_**cat /usr/lib/systemd/system/rabbitmq-server.service**_

_**【判分标准】[0.3分]**_

_**LimitNOFILE=10240**_

_**【判分结束】**_

_**【检测命令4】[0.2分]**_

_**rabbitmqctl status**_

_**【判分标准】[0.2分]**_

_**total_limit,10140**_

_**【判分结束】**_

_**【题目3】Cinder数据加密[2.0分]**_

_**使用自行创建的OpenStack云计算平台，通过相关配置，开启Cinder块存储的数据加密功能，然后创建加密卷类型luks，并配置卷类型luks使用带有512位密钥，Cipher使用aes-xts-plain64，Control Location使用front-end，Provider使用nova.volume.encryptors.luks.LuksEncryptor，最后分别创建两个大小为1G的云硬盘，一个是普通云硬盘，另一个使用加密卷类型。配置完成后提交controller控制节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**cat /etc/nova/nova.conf |grep -Ev '^$|#'**_

_**【判分标准】[1.0分]**_

_**api_class=nova.keymgr.conf_key_mgr.ConfKeyManager**_

_**【判分结束】**_

_**【检测命令1】[1.0分]**_

_**cat /etc/cinder/cinder.conf |grep -Ev '^$|#'**_

_**【判分标准】[1.0分]**_

_**api_class = cinder.keymgr.conf_key_mgr.ConfKeyManager**_

_**【判分结束】**_

_**【题目4】OpenStack Glance镜像压缩[2.0分]**_

_**使用自行搭建的OpenStack平台。在HTTP服务中存在一个镜像为CentOS7.5-compress.qcow2的镜像，请使用qemu相关命令，对该镜像进行压缩，压缩后的镜像命名为chinaskill-js-compress.qcow2并存放在/root目录下。完成后提交controller点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[2.0分]**_

_**du -sh chinaskill-js-compress.qcow2**_

_**【判分标准】[2.0分]**_

_**405M**_

_**【判分结束】**_

_**【题目5】glance对接cinder后端存储[2.0分]**_

_**在自行搭建的OpenStack平台中修改相关参数，使glance可以使用cinder作为后端存储，将镜像存储于cinder卷中。使用cirros-0.3.4-x86_64-disk.img文件创建cirros-image镜像存储于cirros-cinder卷中，通过cirros-image镜像使用cinder卷启动盘的方式进行创建虚拟机。完成后提交修改节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**cat /etc/glance/glance-api.conf  | egrep -v "^#|^$"**_

_**【判分标准】[0.5分]**_

_**show_multiple_locations = True**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**cat /etc/cinder/cinder.conf  | egrep -v "^#|^$"**_

_**【判分标准】[1.0分]**_

_**allowed_direct_url_schemes = cinder||image_upload_use_internal_tenant = True**_

_**【判分结束】**_

_**【检测命令3】[0.5分]**_

_**source /etc/keystone/admin-openrc.sh && openstack image show cirros-image**_

_**【判分标准】[0.5分]**_

_**cinder**_

_**【判分结束】**_

_**【题目6】Linux系统调优[2.0分]**_

_**Linux服务器大并发时，往往需要预先调优Linux参数。默认情况下，Linux最大文件句柄数为1024个。当你的服务器在大并发达到极限时，就会报出“too many open files”。创建一台云主机，修改相关配置，将Linux最大文件句柄数永久修改为65535。配置完成后提交controller点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**ulimit -n**_

_**【判分标准】[1.0分]**_

_**65535**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**cat /etc/security/limits.conf**_

_**【判分标准】[1.0分]**_

_***  soft  nofile  65535||*  hard  nofile  65535**_

_**【判分结束】**_

_**【题目7】数据库高可用集群与负载均衡[2.0分]**_

_**使用赛项提供的OpenStack私有云平台，申请三台CentOS7.9系统的云主机，分别命令为node1、node2、node3，自行在这三个节点上安装数据库服务，数据库密码设置为123456。将这三个节点配置为数据库高可用集群即MariaDB_Galera_Cluster。配置完高可用服务后，安装haproxy负载均衡服务。配置node1节点为负载均衡的窗口，配置负载均衡为轮询算法；HA服务监听的端口为node1节点的3307端口；配置访问三个节点的权重依次为1,2,4。配置完成后提交node1节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**mysql -uroot -p123456 -e "show status like 'wsrep_ready';"**_

_**【判分标准】[0.5分]**_

_**ON**_

_**【判分结束】**_

_**【检测命令2】[0.5分]**_

_**mysql -uroot -p123456 -e "show status like 'wsrep_cluster_size';"**_

_**【判分标准】[0.5分]**_

_**3**_

_**【判分结束】**_

_**【检测命令3】[0.5分]**_

_**netstat -ntpl**_

_**【判分标准】[0.5分]**_

_**3307||9000**_

_**【判分结束】**_

_**【检测命令4】[0.5分]**_

_**cat /etc/haproxy/haproxy.cfg**_

_**【判分标准】[0.5分]**_

_**balance roundrobin||check weight 1||check weight 2||check weight 4**_

_**【判分结束】**_

_**【题目8】私有云镜像排错[3.0分]**_

_**使用赛项提供的error1镜像启动云主机，flavor使用4vcpu/12G内存/100G硬盘。启动后存在错误的私有云平台，错误现象为查看不到image列表，试根据错误信息排查云平台错误，使云平台可以查询到image信息。完成后提交云主机节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[3.0分]**_

_**source /etc/keystone/admin-openrc.sh && openstack image list**_

_**【判分标准】[3.0分]**_

_**9c54622d-a369-4dcf-afee-752ff13f101c**_

_**【判分结束】**_

_**【任务4】OpenStack运维开发任务[15.0分]**_

_**【适用平台】私有云**_

_**【题目1】Ansible部署zabbix服务[3.0分]**_

_**使用赛项提供的OpenStack私有云平台，创建2台系统为centos7.5的云主机，其中一台作为Ansible的母机并命名为ansible，另一台云主机命名为node，通过http服务中的ansible.tar.gz软件包在ansible节点安装Ansible服务；并用这台母机，补全Ansible脚本（在HTTP中下载install_zabbix.tar.gz并解压到/root目录下），补全Ansible脚本使得执行install_zabbix.yaml可以在node节点上完成zabbix服务的安装。完成后提交ansible节点的用户名、密码和IP地址到答题框。（考试系统会连接到ansible节点，执行ansible脚本，准备好环境，以便考试系统访问）**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**cd /root/install_zabbix && ansible-playbook install_zabbix.yaml**_

_**【判分标准】[0.5分]**_

_**failed=0**_

_**【判分结束】**_

_**【检测命令2】[0.5分]**_

_**ansible node -a "netstat -ntpl"**_

_**【判分标准】[0.5分]**_

_**10051**_

_**【判分结束】**_

_**【检测命令3】[0.5分]**_

_**ansible node -a "netstat -ntpl"**_

_**【判分标准】[0.5分]**_

_**3306**_

_**【判分结束】**_

_**【检测命令4】[0.5分]**_

_**cat install_zabbix/roles/zabbix/tasks/main.yaml**_

_**【判分标准】[0.5分]**_

_**create database zabbix**_

_**【判分结束】**_

_**【检测命令5】[0.5分]**_

_**cat install_zabbix/roles/zabbix/tasks/main.yaml**_

_**【判分标准】[0.5分]**_

_**zabbix-server-mysql||zabbix-web-mysql||zabbix-agent||trousers**_

_**【判分结束】**_

_**【检测命令6】[0.5分]**_

_**ansible node -a "curl -L **_[_**http://127.0.0.1/zabbix/setup.php"**_](http://127.0.0.1/zabbix/setup.php%22)

_**【判分标准】[0.5分]**_

_**Installation**_

_**【判分结束】**_

_**【题目2】Ansible部署kafka集群[4.0分]**_

_**使用提供的OpenStack私有云平台，创建4台系统为centos7.5的云主机，其中一台作为Ansible的母机并命名为ansible，另外三台云主机命名为node1、node2、node3，通过附件中的/ansible/ansible.tar.gz软件包在ansible节点安装Ansible服务；使用这一台母机，编写Ansible脚本（在/root目录下创建example目录作为Ansible工作目录，部署的入口文件命名为cscc_install.yaml），编写Ansible脚本使用roles的方式对其他三台云主机进行安装kafka集群的操作（zookeeper和kafka的安装压缩包在gpmall-single.tar.gz压缩包中，将zookeeper和kafka的压缩包解压到node节点的/opt目录下进行安装）。完成后提交ansible节点的用户名、密码和IP地址到答题框。（考试系统会连接到你的ansible节点，去执行ansible脚本，请准备好环境，以便考试系统访问）**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**cd /root/example && ansible-playbook cscc_install.yaml**_

_**【判分标准】[0.5分]**_

_**failed=0||failed=0||failed=0**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**ansible node1,node2,node3 -a "netstat -ntpl"**_

_**【判分标准】[1.0分]**_

_**2181||2181||2181||3888||3888||3888**_

_**【判分结束】**_

_**【检测命令3】[1.0分]**_

_**ansible node1,node2,node3 -a "netstat -ntpl"**_

_**【判分标准】[1.0分]**_

_**2888**_

_**【判分结束】**_

_**【检测命令4】[0.5分]**_

_**ansible node1,node2,node3 -a "sh /opt/zookeeper-3.4.14/bin/zkServer.sh status"**_

_**【判分标准】[0.5分]**_

_**leader**_

_**【判分结束】**_

_**【检测命令5】[1.0分]**_

_**ansible node1,node2,node3 -a "sh /opt/zookeeper-3.4.14/bin/zkServer.sh status"**_

_**【判分标准】[1.0分]**_

_**follower||follower**_

_**【判分结束】**_

_**【题目3】OpenStack Python运维脚本开发：使用Restful API方式创建用户[4.0分]**_

_**在提供的OpenStack私有云平台上，使用T版本的“? HYPERLINK "http://10.24.1.60/dashboard/ngdetails/OS::Glance::Image/ff452418-8afe-4e1a-b0ff-f99bc339c509" ?openstack-python-dev?”镜像创建1台云主机，云主机类型使用4vCPU/12G内存/100G硬盘。该主机中已经默认安装了所需的开发环境，登录默认账号密码为“root/Abc@1234”。使用python request库和OpenStack Restful APIs，在/root目录下，创建api_manager_identity.py文件，编写python代码，代码实现以下任务：**_

_**（1）首先实现查询用户，如果用户名称“user_demo”已经存在，先删除。**_

_**（2）如果不存在“user_demo”，创建该用户，密码设置为“1DY@2022”。**_

_**（3）创建完成后，查询该用户信息，查询的body部分内容控制台输出，同时json格式的输出到文件当前目录下的user_demo.json文件中，json格式要求indent=4。**_

_**编写完成后，提交allinone节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**cat /root/api_manager_identity.py**_

_**【判分标准】[1.0分]**_

_**import requests || requests.post || 5000/v3/auth/tokens || requests.get|| requests.delete||**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**python3 /root/api_manager_identity.py**_

_**【判分标准】[1.0分]**_

_**{'user': || 'links'|| {'self' || 'user_demo'|| 'enabled'**_

_**【判分结束】**_

_**【检测命令3】[2.0分]**_

_**cat /root/user_demo.json**_

_**【判分标准】[2.0分]**_

_**"user" || "enabled" || "password_expires_at"|| "domain_id":|| "name":**_

_**【判分结束】**_

_**【题目4】OpenStack Python运维脚本开发：使用SDK方式创建镜像[4.0分]**_

_**在提供的OpenStack私有云平台上，使用T版本的“? HYPERLINK "http://10.24.1.60/dashboard/ngdetails/OS::Glance::Image/ff452418-8afe-4e1a-b0ff-f99bc339c509" ?openstack-python-dev?”镜像创建1台云主机，云主机类型使用4vCPU/12G内存/100G硬盘。该主机中已经默认安装了所需的开发环境，登录默认账号密码为“root/Abc@1234”。使用“openstacksdk”python库，在/root目录下创建sdk_manager_image.py文件，编写python代码，代码实现以下任务：**_

_**（1）先检查OpenStack镜像“cirros-image”名称是否存在？如果存在，先完成删除该镜像；**_

_**（2）如果不存在“cirros-image”名称，使用文件服务器上“cirros-0.3.4-x86_64-disk.img”文件创建镜像；**_

_**（3）创建完成后，查询该镜像信息，查询的body部分内容控制台输出，同时json格式的输出到文件当前目录下的image_demo.json文件中，json格式要求indent=4。**_

_**编写完成后，提交allinone节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【提交示例】10.18.1.11**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**cat /root/sdk_manager_image.py**_

_**【判分标准】[1.0分]**_

_**import openstack || openstack.connect || image.find_image || image.delete_image || import_image**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**python3 /root/sdk_manager_image.py**_

_**【判分标准】[1.0分]**_

_**disk_format || visibility || min_disk || owner**_

_**【判分结束】**_

_**【检测命令3】[2.0分]**_

_**cat /root/image_demo.json**_

_**【判分标准】[2.0分]**_

_**qcow2 || name|| checksum|| created_at**_

_**【判分结束】**_
