# Kafka 集群搭建

Kafka 消息队列相关技术。

[https://archive.apache.org/dist/kafka/3.6.1/](https://archive.apache.org/dist/kafka/3.6.1/)

# 日志收集平台部署
centos7、 kafka3.6.1、jdk11、

## 环境准备
+ 依赖软件安装

```plain
yum源配置：
    cd /etc/yum.repos.d
    mkdir repo
    mv *.repo repo/
    下载阿里云源：
    curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

下载依赖软件:
yum install epel-release -y
yum install wget vim java-11-openjdk.x86_64  -y
```

+ 配置静态ip地址,修改/etc/sysconfig/network-scripts/ifcfg-ens33

```plain
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
NAME=ens33
UUID=0f3239b9-6ba7-406e-94e8-fa7b680a4d82
DEVICE=ens33
ONBOOT=yes
IPADDR=192.168.223.163
NETMASK=255.255.255.0
GATEWAY=192.168.223.2
DNS1=114.114.114.114
```

+ 配置主机名

```plain
hostnamectl set-hostname kafka1
```

+ 修改/etc/hosts文件，添加主机名和ip地址映射

```plain
192.168.223.161  kafka1
192.168.223.162  kafka2
192.168.223.163  kafka3
```

+ 关闭防火墙与selinux

```plain
关闭防火墙：
  iptables -F
  systemctl stop firewalld
  systemctl disable firewalld
关闭selinux，编辑/etc/selinux/config 文件
  SELINUX=disabled
重启系统：
  reboot
```

## 部署kafka集群
+ 下载kafka

```plain
cd /opt
wget https://archive.apache.org/dist/kafka/3.6.1/kafka_2.13-3.6.1.tgz
```

+ 解压缩

```plain
tar xf kafka_2.13-3.6.1.tgz
cd kafka_2.13-3.6.1
```

+ 修改配置文件,位于kafka目录下config/kraft/server.properties

```plain
#修改节点id，每个节点唯一
node.id=1

#修改控制器投票列表
controller.quorum.voters=1@kafka01:9093,2@kafka02:9093,3@kafka03:9093

#修改监听器和控制器，绑定ip。其中kafka1为主机名，可用本机ip地址代替
listeners=PLAINTEXT://kafka01:9092,CONTROLLER://kafka01:9093

# 侦听器名称、主机名和代理将向客户端公布的端口.(broker 对外暴露的地址)
# 如果未设置，则使用"listeners"的值.
advertised.listeners=PLAINTEXT://kafka01:9092
```

+ _配置文件详解_

```plain
############################# Server Basics #############################

# 此服务器的角色。设置此项将进入KRaft模式(controller 相当于主机、broker 节点相当于从机,主机类似 zk 功能)
process.roles=broker,controller

# 节点 ID
node.id=2

# 全 Controller 列表
controller.quorum.voters=2@192.168.58.130:9093,3@192.168.58.131:9093,4@192.168.58.132:9093

############################# Socket Server Settings #############################

# 套接字服务器侦听的地址.
# 组合节点（即具有`process.roles=broker,controller`的节点）必须至少在此处列出控制器侦听器
# 如果没有定义代理侦听器，那么默认侦听器将使用一个等于java.net.InetAddress.getCanonicalHostName()值的主机名,
# 带有PLAINTEXT侦听器名称和端口9092
#   FORMAT:
#     listeners = listener_name://host_name:port
#   EXAMPLE:
#     listeners = PLAINTEXT://your.host.name:9092
#不同服务器绑定的端口
listeners=PLAINTEXT://192.168.58.130:9092,CONTROLLER://192.168.58.130:9093

# 用于代理之间通信的侦听器的名称(broker 服务协议别名)
inter.broker.listener.name=PLAINTEXT

# 侦听器名称、主机名和代理将向客户端公布的端口.(broker 对外暴露的地址)
# 如果未设置，则使用"listeners"的值.
advertised.listeners=PLAINTEXT://192.168.58.130:9092

# controller 服务协议别名
# 控制器使用的侦听器名称的逗号分隔列表
# 如果`listener.security.protocol.map`中未设置显式映射，则默认使用PLAINTEXT协议
# 如果在KRaft模式下运行，这是必需的。
controller.listener.names=CONTROLLER

# 将侦听器名称映射到安全协议，默认情况下它们是相同的。(协议别名到安全协议的映射)有关更多详细信息，请参阅配置文档.
listener.security.protocol.map=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,SASL_SSL:SASL_SSL

# 服务器用于从网络接收请求并向网络发送响应的线程数
num.network.threads=3

# 服务器用于处理请求的线程数，其中可能包括磁盘I/O
num.io.threads=8

# 套接字服务器使用的发送缓冲区（SO_SNDBUF）
socket.send.buffer.bytes=102400

# 套接字服务器使用的接收缓冲区（SO_RCVBUF）
socket.receive.buffer.bytes=102400

# 套接字服务器将接受的请求的最大大小（防止OOM）
socket.request.max.bytes=104857600

############################# Log Basics #############################

# 存储日志文件的目录的逗号分隔列表(kafka 数据存储目录)
log.dirs=/usr/kafka/kafka_2.13-3.6.1/datas

# 每个主题的默认日志分区数。更多的分区允许更大的并行性以供使用，但这也会导致代理之间有更多的文件。
num.partitions=1

# 启动时用于日志恢复和关闭时用于刷新的每个数据目录的线程数。
# 对于数据目录位于RAID阵列中的安装，建议增加此值。
num.recovery.threads.per.data.dir=1

############################# Internal Topic Settings  #############################
# 组元数据内部主题"__consumer_offsets"和"__transaction_state"的复制因子
# 对于除开发测试以外的任何测试，建议使用大于1的值来确保可用性，例如3.
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1

############################# Log Flush Policy #############################

# 消息会立即写入文件系统，但默认情况下，我们只使用fsync()进行同步
# 操作系统缓存延迟。以下配置控制将数据刷新到磁盘.
# 这里有一些重要的权衡:
#    1. Durability(持久性): 如果不使用复制，未清理的数据可能会丢失
#    2. Latency(延迟): 当刷新发生时，非常大的刷新间隔可能会导致延迟峰值，因为将有大量数据要刷新.
#    3. Throughput(吞吐量): 刷新通常是最昂贵的操作，较小的刷新间隔可能导致过多的寻道.
# 下面的设置允许配置刷新策略，以便在一段时间后或每N条消息（或两者兼有）刷新数据。这可以全局完成，并在每个主题的基础上覆盖

# 强制将数据刷新到磁盘之前要接受的消息数
#log.flush.interval.messages=10000

# 在我们强制刷新之前，消息可以在日志中停留的最长时间
#log.flush.interval.ms=1000

############################# Log Retention Policy #############################

# 以下配置控制日志段的处理。可以将该策略设置为在一段时间后删除分段，或者在累积了给定大小之后删除分段。
# 只要满足这些条件中的任意一个，segment就会被删除。删除总是从日志的末尾开始

# 日志文件因使用年限而有资格删除的最短使用年限
log.retention.hours=168

# 基于大小的日志保留策略。除非剩余的段低于log.retention.bytes，否则将从日志中删除段。独立于log.retention.hours的函数。
#log.retention.bytes=1073741824

# 日志segment文件的最大大小。当达到此大小时，将创建一个新的日志segment
log.segment.bytes=1073741824

# 检查日志segments以查看是否可以根据保留策略删除它们的间隔
log.retention.check.interval.ms=300000
```

+ 创建集群

```plain
cd   /opt/kafka_2.13-3.6.1
# 在其中一台执行，生成集群UUID命令，拿到集群UUID保存在当前tmp_random文件中
/opt/kafka_2.13-3.6.1/bin/kafka-storage.sh random-uuid >tmp_random
# 查看uuid
 [root@chainmaker1 kafka_2.13-3.6.1]# cat tmp_random
z3oq9M4IQguOBm2rt1ovmQ

# 在所有机器上执行，它会初始化存储区域，为 Kafka 集群的元数据存储和后续操作做好准备。z3oq9M4IQguOBm2rt1ovmQ为自己生成的集群uuid

/opt/kafka_2.13-3.6.1/bin/kafka-storage.sh format -t E-cXCfzwTomR1I7Yk02ltw -c /opt/kafka_2.13-3.6.1/config/kraft/server.properties
```

+ 启动

```plain
启动：
/opt/kafka_2.13-3.6.1/bin/kafka-server-start.sh -daemon /usr/kafka/kafka_2.13-3.6.1/config/kraft/server.properties
关闭：
/opt/kafka_2.13-3.6.1/bin/kafka-server-stop.sh
```

    - 命令行启动
+ 使用systemctl管理服务

```plain
## 编辑文件  /usr/lib/systemd/system/kafka.service
  [Unit]
  Description=Apache Kafka server (KRaft mode)
  Documentation=http://kafka.apache.org/documentation.html
  After=network.target
  [Service]
  Type=forking
  User=root
  Group=root
  Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin:/usr/lib/jvm/java-11-openjdk-11.0.23.0.9-2.el7_9.x86_64/bin/"
  ExecStart=/opt/kafka_2.13-3.6.1/bin/kafka-server-start.sh -daemon /opt/kafka_2.13-3.6.1/config/kraft/server.properties
  ExecStop=/opt/kafka_2.13-3.6.1/bin/kafka-server-stop.sh
  Restart=on-failure
  [Install]
  WantedBy=multi-user.target

  #重新加载systemd配置
  systemctl daemon-reload

  #启动kafka服务
  systemctl  start  kafka

  #关闭kafka服务
  systemctl  stop  kafka
```

+ 测试集群[Kraft模式下Kafka脚本的使用-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/1607056)

```plain
# 创建topic
/opt/kafka_2.13-3.6.1/bin/kafka-topics.sh --create --bootstrap-server kafka03:9092 --replication-factor 3 --partitions 3 --topic my_topic

** --replication-factor指定副本因子，--partitions指定分区数，--topic指定主题名称。

# 查看topic
 /opt/kafka_2.13-3.6.1/bin/kafka-topics.sh --list --bootstrap-server kafka03:9092

#创建生产者，发送消息，测试用
/opt/kafka_2.13-3.6.1/bin/kafka-console-producer.sh --broker-list kafka03:9092 --topic my_topic

#创建消费者，获取数据，测试用
/opt/kafka_2.13-3.6.1/bin/kafka-console-consumer.sh --bootstrap-server kafka01:9092 --topic my_topic --from-beginning

```

## 部署filebeat
+ 安装

```plain
1、rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch
2、编辑 vim /etc/yum.repos.d/fb.repo
[elastic-7.x]
name=Elastic repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
3、yum安装
yum  install  filebeat -y

rpm -qa  |grep filebeat  #可以查看filebeat有没有安装  rpm -qa 是查看机器上安装的所有软件包
rpm -ql  filebeat  查看filebeat安装到哪里去了，牵扯的文件有哪些
```

+ 配置，修改配置文件/etc/filebeat/filebeat.yml

```plain
filebeat.inputs:
- type: log
  # Change to true to enable this input configuration.
  enabled: true
  # Paths that should be crawled and fetched. Glob based paths.
  paths:
    - /var/log/nginx/*.log
#==========------------------------------kafka-----------------------------------
output.kafka:
  hosts: ["kafka01:9092","kafka02:9092","kafka03:9093"]
  topic: nginxlog
  keep_alive: 10s
```

+ 创建主题

```plain

/opt/kafka_2.13-3.6.1/bin/kafka-topics.sh --create --bootstrap-server kafka03:9092 --replication-factor 3 --partitions 3 --topic nginxlog
```

+ 启动服务

```plain
systemctl start  filebeat
systemctl enable filebeat  #设置开机自启
```

## nginx反向代理集群搭建
[http://whois.pconline.com.cn/ipJson.jsp?ip=123.123.123.123&json=true](http://whois.pconline.com.cn/ipJson.jsp?ip=123.123.123.123&json=true)

创建反向代理配置文件，代理flask三台后端服务器

```plain
[root@kafka01 ~]# cat /etc/nginx/conf.d/sc.conf
upstream flask {
  server kafka01:5000;
  server kafka02:5000;
  server kafka03:5000;
}

server {
  server_name www.sc.com;
  location / {
  proxy_pass http://flask;
  }

}

```

在三台机器上编写flask应用程序，默认监听5000端口

```plain
from flask import Flask

app = Flask(__name__)

@app.route("/")
def index():
  return "this is flask1"

app.run(host = "0.0.0.0")

```

```plain
celery -A celery-app beat
celery -A celery-app worker -l info -c 4
```

smtp发送测试邮件

```python
import smtplib
from email.mime.text import MIMEText
from email.header import Header
from email.utils import formataddr

# 发件人邮箱
sender = 'cailuobozi@qq.com'
# 发件人邮箱授权码
password = 'deabrqdmxhjhbjdj'
# 收件人邮箱
receiver = '343292019@qq.com'

# 邮件内容
mail_content = "这是一封测试邮件。"
message = MIMEText(mail_content, 'plain', 'utf-8')
message['From'] = formataddr((str(Header("刘彪", 'utf-8')), sender))
message['To'] = formataddr((str(Header("文老师", 'utf-8')), receiver))
message['Subject'] = Header("测试邮件", 'utf-8')

try:
    # 使用SSL加密连接（以QQ邮箱为例）
    smtpObj = smtplib.SMTP_SSL("smtp.qq.com", 465)
    smtpObj.login(sender, password)
    smtpObj.sendmail(sender, [receiver], message.as_string())
    smtpObj.quit()
    print("邮件发送成功")
except smtplib.SMTPException as e:
    print("邮件发送失败：", e)

```
