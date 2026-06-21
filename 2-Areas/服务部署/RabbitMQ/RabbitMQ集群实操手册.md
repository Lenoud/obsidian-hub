# RabbitMQ集群实操手册

RabbitMQ集群实操手册相关技术笔记。
## 2.实战案例——部署RabbitMQ集群
### 2.1 案例目标
（1）了解RabbitMQ服务的安装与配置。

（2）了解RabbitMQ集群的配置架构。

（3）了解RabbitMQ集群的使用。

### 2.2 案例分析
#### 1.规划节点
数据库主从案例的节点规划，见表2-1-1。

表2-1-1 节点规划

| **IP** | **主机名** | **节点** |
| :---: | :---: | :---: |
| 172.30.11.12 | rabbitmq1 | RabbitMQ 磁盘节点 |
| 172.30.11.13 | rabbitmq2 | RabbitMQ 内存节点 |
| 172.30.11.14 | rabbitmq3 | RabbitMQ 内存节点 |

#### 2.基础准备
使用OpenStack平台创建三台云主机进行实验，云主机镜像使用提供的CentOS_7.5_x86_64_XD.qcow2镜像，flavor使用1核/2G内存/20G硬盘，自行配置网络并使用远程连接工具连接云主机。节点规划表中的IP地址为作者的IP地址，在进行实操案例的时候，按照自己的环境规划网络与IP地址。

### 2.3 案例实施
#### 1.基础环境安装
##### （1）修改主机名
使用远程连接工具CRT连接到172.30.11.12、172.30.11.13、172.30.11.14这三台虚拟机，并对这三台虚拟机进行修改主机名的操作，172.30.11.12主机名修改为rabbitmq1；172.30.11.13主机名修改为rabbitmq2；172.30.11.14主机名修改为rabbitmq3。命令如下：

rabbitmq1节点：

[root@localhost ~]# hostnamectl set-hostname rabbitmq1

[root@localhost ~]# logout

[root@rabbitmq1 ~]# hostnamectl

   Static hostname: rabbitmq1

         Icon name: computer-vm

           Chassis: vm

        Machine ID: 622ba110a69e24eda2dca57e4d306baa

           Boot ID: 859720a14f8f4b5e836f5a0fae7097e0

    Virtualization: kvm

  Operating System: CentOS Linux 7 (Core)

       CPE OS Name: cpe:/o:centos:centos:7

            Kernel: Linux 3.10.0-862.2.3.el7.x86_64

      Architecture: x86-64

rabbitmq2节点：

[root@localhost ~]# hostnamectl set-hostname rabbitmq2

[root@localhost ~]# logout

[root@rabbitmq2 ~]# hostnamectl

   Static hostname: rabbitmq2

         Icon name: computer-vm

           Chassis: vm

        Machine ID: 622ba110a69e24eda2dca57e4d306baa

           Boot ID: 5e41c48c85704016ad0bd940500cc255

    Virtualization: kvm

  Operating System: CentOS Linux 7 (Core)

       CPE OS Name: cpe:/o:centos:centos:7

            Kernel: Linux 3.10.0-862.2.3.el7.x86_64

      Architecture: x86-64

rabbitmq3节点：

[root@localhost ~]# hostnamectl set-hostname rabbitmq2

[root@localhost ~]# logout

[root@rabbitmq3 ~]# hostnamectl

   Static hostname: rabbitmq3

         Icon name: computer-vm

           Chassis: vm

        Machine ID: 622ba110a69e24eda2dca57e4d306baa

           Boot ID: eef9314b97ac4331bfa423f1d3de67bf

    Virtualization: kvm

  Operating System: CentOS Linux 7 (Core)

       CPE OS Name: cpe:/o:centos:centos:7

            Kernel: Linux 3.10.0-862.2.3.el7.x86_64

      Architecture: x86-64

##### （2）关闭防火墙及SELinux服务
三个节点关闭防火墙firewalld及SELinux服务，命令如下：

```bash
setenforce 0
```

```bash
systemctl stop firewalld
```

##### （3）配置hosts文件
三个节点配置/etc/hosts文件，修改为如下：

127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4

::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

172.30.11.12  rabbitmq1

172.30.11.13  rabbitmq2

172.30.11.14  rabbitmq3

##### （4）配置YUM源
三个节点均使用提供的rabbitmq-repo.tar.gz的压缩包，上传至虚拟机的/root目录下，解压并放在/opt目录下，进入/etc/yum.repos.d目录下，将原来的repo文件移除，新建local.repo文件并编辑内容，具体操作命令如下：

```bash
tar -zxvf rabbitmq-repo.tar.gz -C /opt/
```

```bash
cd /etc/yum.repos.d/
```

```bash
mv * /media/
```

```bash
vi local.repo
```

```bash
cat local.repo
```

[rabbitmq]

name=rabbitmq

baseurl=file:///opt/rabbitmq-repo

gpgcheck=0

enabled=1

查看配置的YUM源是否可用，命令如下：

```bash
yum repolist
```

Loaded plugins: fastestmirror

Loading mirror speeds from cached hostfile

repo id                   repo name                        status

rabbitmq                 rabbitmq                          26

repolist: 26

查看到repolist数量，即YUM源配置成功。

##### （5）安装RabbitMQ服务并启动
配置完毕后，三个节点安装RabbitMQ服务，命令如下：

```bash
yum install -y rabbitmq-server
```

rabbitmq1节点启动RabbitMQ服务并查看服务状态，命令如下：

```bash
systemctl start rabbitmq-server
```

#[root@rabbitmq1 ~]# systemctl status rabbitmq-server

● rabbitmq-server.service - RabbitMQ broker

   Loaded: loaded (/usr/lib/systemd/system/rabbitmq-server.service; disabled; vendor preset: disabled)

   Active: active (running) since Tue 2020-10-20 05:48:17 UTC; 1h 7min ago

  Process: 13641 ExecStop=/usr/lib/rabbitmq/bin/rabbitmqctl stop (code=exited, status=0/SUCCESS)

 Main PID: 13685 (beam)

   CGroup: /system.slice/rabbitmq-server.service

           ├─13685 /usr/lib64/erlang/erts-5.10.4/bin/beam -W w -K true -A30 -P 1048576 -- -root /usr/lib64/erlang -progname erl -- -home /var/lib/rabbitmq -- -pa /usr/lib/rabbitmq/lib/ra...

           ├─13757 inet_gethost 4

           └─13758 inet_gethost 4

Oct 20 05:48:14 rabbitmq1 systemd[1]: Starting RabbitMQ broker...

##### （6）配置界面访问
RabbitMQ提供了一个非常友好的图形化监控页面插件（rabbitmq_management），让我们可以一目了然看见Rabbit的状态或集群状态。启用图形化页面插件的具体命令如下：

[root@rabbitmq1 ~]# rabbitmq-plugins enable rabbitmq_management

The following plugins have been enabled:

  mochiweb

  webmachine

  rabbitmq_web_dispatch

  amqp_client

  rabbitmq_management_agent

  rabbitmq_management

Plugin configuration has changed. Restart RabbitMQ for changes to take effect.

启用图形化界面后，需要重启RabbitMQ服务，命令如下：

[root@rabbitmq1 ~]# service rabbitmq-server restart

Redirecting to /bin/systemctl restart rabbitmq-server.service

##### （7）使用界面查看
在开启了图形化监控页面之后，能通过网页访问，访问端口为15672，使用命令查看端口启动情况，命令如下：

[root@rabbitmq1 ~]# netstat -ntpl

Active Internet connections (only servers)

Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name

tcp        0      0 0.0.0.0:25672           0.0.0.0:*               LISTEN      13685/beam

tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      500/rpcbind

tcp        0      0 0.0.0.0:4369            0.0.0.0:*               LISTEN      11320/epmd

tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1162/sshd

tcp        0      0 0.0.0.0:15672           0.0.0.0:*               LISTEN      13685/beam

tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      932/master

tcp6       0      0 :::5672                 :::*                    LISTEN      13685/beam

tcp6       0      0 :::111                  :::*                    LISTEN      500/rpcbind

tcp6       0      0 :::4369                 :::*                    LISTEN      11320/epmd

tcp6       0      0 :::22                   :::*                    LISTEN      1162/sshd

tcp6       0      0 ::1:25                  :::*                    LISTEN      932/master

可以看到15672端口已开放，打开浏览器，输入rabbitmq1节点的ip加端口15672（[http://172.30.11.12:15672](http://172.30.11.12:15672)）访问RabbitMQ监控界面，使用用户名：guest，密码：guest登录，登录后如下图所示：

![[1699325413160-a5aa8803-491b-45ca-a9ca-bfdf4239af9b.png]]

#### 2.配置RabbitMQ集群服务
##### （1）配置节点间的通信
Rabbitmq的集群是依附于erlang集群来工作的，所以必须先构建起一个erlang集群。erlang集群中各节点是由magic cookie来实现的，每个节点上要保持相同的.erlang.cookie文件，这个cookie存放在/var/lib/rabbitmq/.erlang.cookie中，文件是400的权限。必须保证各节点cookie一致,不然节点之间就无法通信。

查看rabbitmq1节点的.erlang.cookie文件，并将该文件复制到rabbitmq2和rabbitmq3节点的/var/lib/rabbitmq/目录下，命令如下：

[root@rabbitmq1 ~]# cat /var/lib/rabbitmq/.erlang.cookie

EZYGPUJOTSESXPAUFMWO

[root@rabbitmq1 ~]# scp /var/lib/rabbitmq/.erlang.cookie root@rabbitmq2:/var/lib/rabbitmq/

[root@rabbitmq1 ~]# scp /var/lib/rabbitmq/.erlang.cookie root@rabbitmq3:/var/lib/rabbitmq/

将.erlang.cookie文件传至rabbitmq2和rabbitmq3节点后，需要修改该文件的用户与用户组，命令如下：

[root@rabbitmq2 rabbitmq]# chown rabbitmq:rabbitmq .erlang.cookie

[root@rabbitmq3 rabbitmq]# chown rabbitmq:rabbitmq .erlang.cookie

##### （2）配置节点加入集群
在rabbitmq2、rabbitmq3节点执行如下命令，将这两个节点作为RAM节点加入到RabbitMQ集群中，具体命令如下：

rabbitmq2节点：

[root@rabbitmq2 rabbitmq]# rabbitmqctl stop_app

Stopping node rabbit@rabbitmq2 ...

...done.

[root@rabbitmq2 rabbitmq]# rabbitmqctl join_cluster --ram rabbit@rabbitmq1

Clustering node rabbit@rabbitmq2 with rabbit@rabbitmq1 ...

...done.

[root@rabbitmq2 rabbitmq]# rabbitmqctl start_app

Starting node rabbit@rabbitmq2 ...

...done.

rabbitmq3节点：

[root@rabbitmq3 rabbitmq]# rabbitmqctl stop_app

Stopping node rabbit@rabbitmq3 ...

...done.

[root@rabbitmq3 rabbitmq]# rabbitmqctl join_cluster --ram rabbit@rabbitmq1

Clustering node rabbit@rabbitmq3 with rabbit@rabbitmq1 ...

...done.

[root@rabbitmq3 rabbitmq]# rabbitmqctl start_app

Starting node rabbit@rabbitmq3 ...

...done.

默认rabbitmq启动后是磁盘节点，在这个cluster命令下，rabbitmq2和rabbitmq3是内存节点，rabbitmq1是磁盘节点。

如果要使rabbitmq2、rabbitmq3都是磁盘节点，去掉--ram参数即可。

如果想要更改节点类型，可以使用命令rabbitmqctl change_cluster_node_type disc(ram),前提是必须停掉rabbit应用。

##### （3）配置RAM节点启用界面
在rabbitmq2和rabbitmq3节点上启用rabbitmq_management，命令如下：

rabbitmq2节点：

[root@rabbitmq2 rabbitmq]# rabbitmq-plugins enable rabbitmq_management

The following plugins have been enabled:

  mochiweb

  webmachine

  rabbitmq_web_dispatch

  amqp_client

  rabbitmq_management_agent

  rabbitmq_management

Plugin configuration has changed. Restart RabbitMQ for changes to take effect.

[root@rabbitmq2 rabbitmq]# service rabbitmq-server restart

Redirecting to /bin/systemctl restart rabbitmq-server.service

rabbitmq3节点：

[root@rabbitmq3 rabbitmq]# rabbitmq-plugins enable rabbitmq_management

The following plugins have been enabled:

  mochiweb

  webmachine

  rabbitmq_web_dispatch

  amqp_client

  rabbitmq_management_agent

  rabbitmq_management

Plugin configuration has changed. Restart RabbitMQ for changes to take effect.

[root@rabbitmq3 rabbitmq]# service rabbitmq-server restart

Redirecting to /bin/systemctl restart rabbitmq-server.service

启用rabbitmq2节点和rabbitmq3节点的监控界面后，登录[http://172.30.11.12:15672](http://172.30.11.12:15672)，查看监控界面，如下图所示：

![[1699325413554-f6f153e1-c08e-4f0d-89ee-040fc313b756.png]]

可以看到rabbitmq1节点为数据节点，rabbitmq2和rabbitmq3节点为RAM内存节点。

##### （4）RabbitMQ集群常用命令

查看插件打开情况:

```bash
rabbitmq-plugins list
```

输出示例:

```text
[e] amqp_client                       3.3.5
[ ] cowboy                            0.5.0-rmq3.3.5-git4b93c2d
[ ] eldap                             3.3.5-gite309de4
[e] mochiweb                          2.7.0-rmq3.3.5-git680dba8
[ ] rabbitmq_amqp1_0                  3.3.5
[ ] rabbitmq_auth_backend_ldap        3.3.5
[ ] rabbitmq_auth_mechanism_ssl       3.3.5
[ ] rabbitmq_consistent_hash_exchange 3.3.5
[ ] rabbitmq_federation               3.3.5
[ ] rabbitmq_federation_management    3.3.5
[E] rabbitmq_management               3.3.5
[e] rabbitmq_management_agent         3.3.5
[ ] rabbitmq_management_visualiser    3.3.5
[ ] rabbitmq_mqtt                     3.3.5
[ ] rabbitmq_shovel                   3.3.5
[ ] rabbitmq_shovel_management        3.3.5
[ ] rabbitmq_stomp                    3.3.5
[ ] rabbitmq_test                     3.3.5
[ ] rabbitmq_tracing                  3.3.5
[e] rabbitmq_web_dispatch             3.3.5
[ ] rabbitmq_web_stomp                3.3.5
[ ] rabbitmq_web_stomp_examples       3.3.5
[ ] sockjs                            0.3.4-rmq3.3.5-git3132eb9
[e] webmachine                        1.10.3-rmq3.3.5-gite9359c7
```

启动 / 关闭监控管理器:

```bash
rabbitmq-plugins enable rabbitmq_management       # 启动
rabbitmq-plugins disable rabbitmq_management      # 关闭
```

队列管理:

```bash
rabbitmqctl list_queues        # 查看所有队列
rabbitmqctl reset              # 清除所有队列(危险!)
```

用户管理:

```bash
rabbitmqctl list_users                                # 查看用户
rabbitmqctl status                                    # 查看节点状态
rabbitmqctl add_user admin admin                      # 新增用户 admin,密码 admin
rabbitmqctl delete_user admin                         # 删除用户
rabbitmqctl change_password admin admin123            # 修改密码
rabbitmqctl set_user_tags admin administrator monitoring policymaker management   # 设置角色
```

权限管理:

```bash
rabbitmqctl set_permissions -p VHostPath admin ConfP WriteP ReadP   # 设置权限
rabbitmqctl list_permissions [-p VHostPath]                          # 查询所有权限
rabbitmqctl list_user_permissions admin                              # 查指定用户权限
rabbitmqctl clear_permissions [-p VHostPath] admin                   # 清除权限
```

查看集群状态:

```bash
rabbitmqctl cluster_status
```

输出示例:

```text
Cluster status of node rabbit@rabbitmq1 ...
[{nodes,[{disc,[rabbit@rabbitmq1]},{ram,[rabbit@rabbitmq3,rabbit@rabbitmq2]}]},
 {running_nodes,[rabbit@rabbitmq3,rabbit@rabbitmq2,rabbit@rabbitmq1]},
 {cluster_name,<<"rabbit@rabbitmq1">>},
 {partitions,[]}]
...done.
```

可看到 rabbitmq1 为 disc 磁盘节点,rabbitmq2、rabbitmq3 为 RAM 内存节点。
