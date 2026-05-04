### 应用部署：堡垒机部署[0.5 分]
使用提供的 OpenStack 平台申请一台 CentOS7.9 的云主机，使用提供的软件包安装JumpServer 堡垒机服务，并配置使用该堡垒机对接自己安装的 controller 和 compute 节点。完成后提交 JumpServer 节点的用户名、密码和 IP 地址到答题框。

1.检查堡垒机部署正确计 0.5 分

（1）修改主机名

远程连接堡垒机节点，修改节点的主机名为jumpserver，修改主机名后，执行bash命令 或者重启刷新界面，如下所示：

[root@jumpserver ~]# hostnamectl set-hostname jumpserver

（2）关闭防火墙与SELinux

将节点的防火墙与SELinux关闭，并设置永久关闭SELinux，命令如下：

[root@jumpserver ~]# setenforce 0

[root@jumpserver ~]# sed  -i  s#SELINUX=enforcing#SELINUX=disabled#   /etc/selinux/config

[root@jumpserver ~]# iptables -F

[root@jumpserver ~]# iptables -X

[root@jumpserver ~]# iptables -Z

[root@jumpserver ~]# /usr/sbin/iptables-save

（3）配置本地Yum源

解压软件包jumpserver.tar.gz至/root目录下，命令如下：

[root@jumpserver ~]# tar -zxvf jumpserver.tar.gz -C /opt/

[root@jumpserver ~]# ls /opt/

compose  config  docker  docker.service  images  jumpserver-repo  static.env

[root@jumpserver ~]# rm -rf /etc/yum.repos.d/*

[root@jumpserver ~]# cat >> /etc/yum.repos.d/jumpserver.repo << EOF

[jumpserver]

name=jumpserver

baseurl=file:///opt/jumpserver-repo

gpgcheck=0

enabled=1

EOF

[root@jumpserver ~]# yum repolist

repo id     repo name       status

jumpserver  jumpserver      2

（4）安装依赖环境

安装Python数据库，命令如下：

[root@jumpserver ~]# yum install python2 -y

安装配置Docker环境，命令如下：

[root@jumpserver ~]# cp -rfv /opt/docker/* /usr/bin/

[root@jumpserver ~]# chmod 775 /usr/bin/docker*

[root@jumpserver ~]# cp -rfv /opt/docker.service /etc/systemd/system/

[root@jumpserver ~]# chmod 755 /etc/systemd/system/docker.service

[root@jumpserver ~]# systemctl daemon-reload

[root@jumpserver ~]# systemctl enable docker --now

验证服务状态，命令如下：

[root@jumpserver ~]# docker --version

Docker version 18.06.3-ce, build d7080c1

[root@jumpserver ~]# docker-compose --version

docker-compose version 1.27.4, build 40524192

（5）安装Jumpserver服务

加载Jumpserver服务组件镜像，命令如下：

[root@jumpserver ~]# cd /opt/images/

[root@jumpserver images]# sh load.sh

创建Jumpserver服务组件目录，命令如下：

[root@jumpserver images]# mkdir -p /opt/jumpserver/{core,koko,lion,mysql,nginx,redis}

[root@jumpserver images]# cp -rf /opt/config /opt/jumpserver/

生效环境变量static.env，使用所提供的脚本up.sh启动Jumpserver服务，命令如下：

[root@jumpserver images]# cd ..

[root@jumpserver opt]# cd compose/

[root@jumpserver compose]# source ../static.env

[root@jumpserver compose]# sh up.sh

Creating network "jms_net" with driver "bridge"

Creating jms_mysql ... done

Creating jms_redis ... done

Creating jms_core  ... done

Creating jms_celery ... done

Creating jms_luna   ... done

Creating jms_lion   ... done

Creating jms_lina   ... done

Creating jms_nginx  ... done

Creating jms_koko   ... done

如果报错，那就是容器还在启动中，等那个容器起来了在继续跑

使用谷歌浏览器访问[http://10.24.193.142](http://10.24.193.142)，Jumpserver Web登录（admin/admin），如图1所示：

![[1685611123413-9dd18728-670b-4736-97f6-a9ad7008cf4a.png]]图1 Web登录

登录成功后，会提示设置新密码，如图2所示：

![[1685611123667-1223fc09-426f-45dd-8f5c-64832c180311.png]]图2 修改密码

登录平台后，单击页面右上角下拉菜单切换中文字符设置，如图3所示：

![[1685611124112-d223e095-6a6d-4fb3-86c5-dd45bff55acf.png]]图3 登录成功

至此Jumpserver安装完成。

（6）管理资产

使用管理员admin用户登录Jumpserver管理平台，单击左侧导航栏，展开“资产管理”项目，选择“管理用户”，单击右侧“创建”按钮，如图4所示：

![[1685611124484-011b6fc5-0c38-4f13-a62a-87338c8b14f9.png]]图4 管理用户

创建远程连接用户，用户名为root密码为“Abc@1234”，单击“提交”按钮进行创建，如图5所示：

![[1685611124713-91c1e633-043a-4ddc-9dad-231fe7fcb180.png]]图5 创建管理用户

选择“系统用户”，单击右侧“创建”按钮，创建系统用户，选择主机协议“SSH”，设置用户名root，密码为服务器SSH密码并单击“提交”按钮，如图6所示：

![[1685611124993-8ee028f3-74b2-4996-9ce5-88da2ad885a8.png]]图6 创建系统用户

单击左侧导航栏，展开“资产管理”项目，选择“资产列表”，单击右侧“创建”按钮，如图7所示：

![[1685611125363-695aba38-1a7d-4098-9d33-929f7e25a08d.png]]图7 管理资产

创建资产，将云平台主机（controller）加入资产内，如图8、图9所示：

![[1685611125837-9b41f0fc-cf0d-46b1-97d7-b55952ab321c.png]]图8 创建资产controller

![[1685611126190-40716a39-291f-4ad5-bb3b-3c63cc55bdff.png]]图9 创建成功

（7）资产授权

单击左侧导航栏，展开“权限管理”项目，选择“资产授权”，单击右侧“创建”按钮，创建资产授权规则，如图10所示：

![[1685611126520-b8487295-7868-46f3-a110-cc1de2003a8b.png]]图10 创建资产授权规则

（8）测试连接

单击右上角管理员用户下拉菜单，选择“用户界面”，如图11所示：

![[1685611126857-9c7843de-c00f-46e9-90df-c9763d6d5f4d.png]]图11 创建资产授权规则

如果未出现Default项目下的资产主机，单击收藏夹后“刷新”按钮进行刷新，如图12所示：

![[1685611127241-8f40527d-3b5f-4d68-8924-b554d948c91f.png]]图12 查看资产

单击左侧导航栏，选择“Web终端”进入远程连接页面，如图13所示：

![[1685611127598-1c1cd2a7-1844-4bcf-874e-a99810f91485.png]]图13 进入远程连接终端

单击左侧Default，展开文件夹，单击controller主机，右侧成功连接主机，如图14所示：

![[1685611127925-ef78272d-fd57-4070-a928-a75101fc61ef.png]]图14 测试连接

至此OpenStack对接堡垒机案例操作成功。

后面compute节点的添加也是相同操作

![[1685611128389-8fe37461-f793-47fb-910a-07abb51afc9f.png]]
