## jdk8_zk_hadoop_hbase
```bash
#!/bin/bash
# 确保脚本以 root 用户权限运行
if [ "$(id -u)" -ne "0" ]; then
    echo "请以 root 用户运行此脚本"
    exit 1
fi

#开始安装
echo -e "\e[35mroot目录下的压缩包如下:\e[0m"
ls | grep tar.gz

echo -e "\e[31m请将以下压缩包放到root目录下:\e[0m"
echo -e "jdk-8u144-linux-x64.tar.gz\nhbase-2.4.9-bin.tar.gz\napache-zookeeper-3.7.0-bin.tar.gz\nhadoop-3.2.2.tar.gz\n"
echo -e "\e[33m您确定要继续执行这个操作吗？ (yes/no)\e[0m"
read -r control

if [[ "$control" == "no" ]]; then
    echo -e "\e[31m操作已取消。\e[0m"
    exit 1
fi
echo -e "\e[32m继续执行操作...\e[0m"

echo "修改hostname:"
read -r hostname
hostnamectl set-hostname $hostname
echo -e "\e[32m修改hostname为：$hostname \e[0m"

echo "关闭firewalld防火墙"
systemctl stop firewalld
systemctl disable firewalld
systemctl status firewalld
echo -e "\e[32m关闭firewalld防火墙完成\e[0m"

echo "当前IP地址:"
ip a | grep scope | awk {'print $2'} | cut -d'/' -f1
ifconfig | grep "inet "|grep 'broadcast' | awk '{print $2}'

echo "设置hosts,输入HBase01 IP地址:"
read -r host1
echo "设置hosts,输入HBase02 IP地址:"
read -r host2
echo "设置hosts,输入HBase03 IP地址:"
read -r host3
cat >> /etc/hosts << EOF
$host1 HBase01
$host2 HBase02
$host3 HBase03
EOF
echo -e "\e[32m设置hosts完成\e[0m"

echo "设置zkmyid"
read -r zkmyid

echo "解压压缩包"
tar -zxvf /root/jdk-8u144-linux-x64.tar.gz -C /opt/
tar -zxvf /root/apache-zookeeper-3.7.0-bin.tar.gz -C /opt/
tar -zxvf /root/hadoop-3.2.2.tar.gz -C /opt/
tar -zxvf /root/hbase-2.4.9-bin.tar.gz -C /opt/
echo -e "\e[32m解压压缩包完成\e[0m"

echo "重命名"
mv /opt/jdk1.8.0_144 /opt/jdk1.8
mv /opt/apache-zookeeper-3.7.0-bin /opt/zookeeper-3.7.0

echo -e "\e[32mprofile环境变量配置\e[0m"
cat >> /etc/profile << EOF

export JAVA_HOME=/opt/jdk1.8
export PATH=\$PATH:\$JAVA_HOME/bin

export HADOOP_HOME=/opt/hadoop-3.2.2
export PATH=\$PATH:\$HADOOP_HOME/bin:\$HADOOP_HOME/sbin

export ZK_HOME=/opt/zookeeper-3.7.0
export PATH=\$PATH:\$ZK_HOME/bin

export HBASE_HOME=/opt/hbase-2.4.9
export PATH=\$PATH:\$HBASE_HOME/bin

EOF

echo -e "\e[32m修改hadoop配置文件\e[0m"
cat >> /opt/hadoop-3.2.2/etc/hadoop/hadoop-env.sh << EOF

export JAVA_HOME=/opt/jdk1.8/	# 这里必须和bashrc中的设置保持一致
export HADOOP_HOME=/opt/hadoop-3.2.2/ # hadoop安装位置
# 定义HDFS和yarn在root用户上运行
export	HDFS_NAMENODE_USER=root
export	HDFS_DATANODE_USER=root
export	HDFS_SECONDARYNAMENODE_USER=root
export	YARN_RESOURCEMANAGER_USER=root
export	YARN_NODEMANAGER_USER=root
# 定义hadoop的log路径和lib路径（若无此路径则要另外创建）
export	HADOOP_PID_DIR=/opt/hadoop_pid/
export	HADOOP_LOG_DIR=/opt/hadoop3.2.2-logs/

EOF

# 创建hadoop需要的目录
echo "创建hadoop需要的目录"
mkdir -p /opt/hadoop_pid/
mkdir -p /opt/hadoop3.2.2-logs/

sed -i "s#<configuration>##g" /opt/hadoop-3.2.2/etc/hadoop/core-site.xml
sed -i "s#</configuration>##g" /opt/hadoop-3.2.2/etc/hadoop/core-site.xml

cat >> /opt/hadoop-3.2.2/etc/hadoop/core-site.xml <<  EOF

<configuration>
  <property>
    <name>hadoop.tmp.dir</name>
    <value>/opt/data/hadoop/tmp</value>
  </property>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://HBase01:9820</value>
  </property>
</configuration>

EOF

sed -i "s#</configuration>##g" /opt/hadoop-3.2.2/etc/hadoop/hdfs-site.xml
sed -i "s#<configuration>##g" /opt/hadoop-3.2.2/etc/hadoop/hdfs-site.xml

cat >> /opt/hadoop-3.2.2/etc/hadoop/hdfs-site.xml << EOF

<configuration>
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>

  <property>
    <name>dfs.namenode.name.dir</name>
    <value>/opt/data/hadoop/namenode</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>/opt/data/hadoop/datanode</value>
  </property>

  <property>
    <name>dfs.namenode.http-addrees</name>
    <value>HBase01:9870</value>
  </property>

  <property>
    <name>dfs.namenode.secondary.http-address</name>
    <value>HBase02:9868</value>
  </property>

</configuration>
EOF

sed -i "s#</configuration>##g" /opt/hadoop-3.2.2/etc/hadoop/mapred-site.xml
sed -i "s#<configuration>##g" /opt/hadoop-3.2.2/etc/hadoop/mapred-site.xml

cat >> /opt/hadoop-3.2.2/etc/hadoop/mapred-site.xml << EOF

<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>

EOF

sed -i "s#</configuration>##g" /opt/hadoop-3.2.2/etc/hadoop/yarn-site.xml
sed -i "s#<configuration>##g" /opt/hadoop-3.2.2/etc/hadoop/yarn-site.xml

cat >> /opt/hadoop-3.2.2/etc/hadoop/yarn-site.xml << EOF

<configuration>
   <property>
           <name>yarn.resourcenodemanager.hostname</name>
           <value>HBase01</value>
   </property>

   <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
   </property>
</configuration>

EOF

cat > /opt/hadoop-3.2.2/etc/hadoop/workers << EOF
HBase02
HBase03
EOF

echo -e "\e[32m修改Hbase配置文件\e[0m"
#修改Hbase配置文件
sed -i "2i export JAVA_HOME=/opt/jdk1.8"  /opt/hbase-2.4.9/conf/hbase-env.sh
sed -i "3i export HBASE_MANAGES_ZK=false"  /opt/hbase-2.4.9/conf/hbase-env.sh

cp /opt/hbase-2.4.9/conf/hbase-site.xml /opt/hbase-2.4.9/conf/hbase-site.xml.bak
cat > /opt/hbase-2.4.9/conf/hbase-site.xml << EOF
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <!--启动集群模式-->
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>

  <!--HBase的数据保存在HDFS对应目录-->
  <property>
    <name>hbase.rootdir</name>
    <value>hdfs://HBase01:9820/hbase_fully</value>
  </property>

  <!--配置ZK的地址-->
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>HBase01:2181,HBase02:2181,HBase03:2181</value>
  </property>

  <!--zookeeper数据目录-->
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/opt/data/zookeeper/zkdata</value>
  </property>

  <property>
    <name>zookeeper.znode.parent</name>
    <value>/hbase-fully</value>
  </property>

  <!--主节点和从节点允许的最大时间误差-->
  <property>
    <name>hbase.master.maxclockskew</name>
    <value>180000</value>
  </property>

</configuration>
EOF

cat > /opt/hbase-2.4.9/conf/regionservers << EOF
HBase02
HBase03
EOF

echo -e "\e[32m配置zookeeper\e[0m"
mkdir -p /opt/data/zookeeper/zkdata
echo $zkmyid > /opt/data/zookeeper/zkdata/myid

cat >>  /opt/zookeeper-3.7.0/conf/zoo.cfg << EOF
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/opt/data/zookeeper/zkdata
clientPort=2181
server.1=HBase01:2888:3888
server.2=HBase02:2888:3888
server.3=HBase03:2888:3888

EOF

echo -e "\e[32m配置修改完毕\n\n\n\e[0m"

#配置时间同步服务器
chronyFILE=/etc/chrony.conf

# 第1层判断
if test -f "$chronyFILE"; then
    echo "$chronyFILE 时间同步服务器存在"

    # 第2层判断
    echo -e "\e[31m是否执行时间同步配置脚本 yes/no。\e[0m"
    read -r chronysh
    if [[ "$chronysh" == "yes" ]]; then

        # 第3层判断
        echo -e "\e[32m此服务器是否作为 ntp 服务端（aliyun） yes/no\e[0m"
        read -r ntpserver
        if [[ "$ntpserver" == "yes" ]]; then
            ###########################################
            echo -e "\e[32m配置为 ntpServer\e[0m"

            # 备份原始配置文件
            cp /etc/chrony.conf /etc/chrony.conf.bak

            # 注释掉对应配置
            sed -i 's/^pool/#pool/g' /etc/chrony.conf
            sed -i 's/^allow/#allow/g' /etc/chrony.conf
            sed -i 's/^server/#server/g' /etc/chrony.conf
            # 配置新的 NTP 服务器
            cat >> /etc/chrony.conf <<EOF
server ntp1.aliyun.com iburst
server ntp2.aliyun.com iburst
server ntp3.aliyun.com iburst
server ntp4.aliyun.com iburst
allow all
EOF

            # 重启 Chrony 服务并检查 NTP 源
            systemctl restart chronyd
            sleep 1s
            chronyc sources -v
            ###########################################
        else
            ###########################################
            echo -e "\e[31m配置为 ntpClient\e[0m"

            # 备份原始配置文件
            cp /etc/chrony.conf /etc/chrony.conf.bak
            # 注释掉所有的 pool 行
            sed -i 's/^pool/#pool/g' /etc/chrony.conf
            sed -i 's/^server/#server/g' /etc/chrony.conf
            # 配置新的 NTP 服务器
            cat >> /etc/chrony.conf <<EOF
server HBase01 iburst
EOF

            # 重启 Chrony 服务并检查 NTP 源
            systemctl restart chronyd
            sleep 1s
            chronyc sources -v
            ###########################################
        fi
    else
        echo -e "\e[31m操作已取消。\e[0m"
        exit 1
    fi
else
    echo "$chronyFILE 不存在，请检查 chronyd 服务器是否安装"
fi

#生成密钥文件
keyFile=~/.ssh/id_rsa
if test -f "$keyFile"; then
  echo -e "\e[31mssh密钥文件存在\e[0m"
else
  echo -e "\e[31m生成ssh密钥文件\e[0m"
  ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
fi

echo -e "\e[31m
配置完毕
HBaseWeb端口Hbase01IP:16010
HadoopWeb端口Hbase01IP:9870
所有安装文件及其数据存放于/opt/目录
JPS执行结果
\e[0m"
jps

echo -e "\e[34m
#分发主节点的ssh密钥
ssh-copy-id $host1
ssh-copy-id $host2
ssh-copy-id $host3

#所有节点初始化hdfs
hdfs namenode -format
ssh root@Hbase02 'source /etc/profile && hdfs namenode -format'
ssh root@Hbase03 'source /etc/profile && hdfs namenode -format'

#主节点hadoop集群启动
start-dfs.sh
start-yarn.sh

#所有节点zookeeper启动
zkServer.sh  start
zkServer.sh  status
ssh root@Hbase02 'source /etc/profile && zkServer.sh  start'
ssh root@Hbase03 'source /etc/profile && zkServer.sh  start'

#主节点hbase集群启动
start-hbase.sh
\e[0m"
```

##

## /opt/hadoop-3.2.2/etc/hadoop/core-site.xml
```xml
cat >> /opt/hadoop-3.2.2/etc/hadoop/core-site.xml <<  EOF

<configuration>
  <property>
    <name>hadoop.tmp.dir</name>
    <value>/opt/data/hadoop/tmp</value>
  </property>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://HBase01:9820</value>
  </property>
</configuration>

EOF
```

## /opt/hadoop-3.2.2/etc/hadoop/hdfs-site.xml
```xml
cat >> /opt/hadoop-3.2.2/etc/hadoop/hdfs-site.xml << EOF

<configuration>
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>

  <property>
    <name>dfs.namenode.name.dir</name>
    <value>/opt/data/hadoop/namenode</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>/opt/data/hadoop/datanode</value>
  </property>

  <property>
    <name>dfs.namenode.http-addrees</name>
    <value>HBase01:9870</value>
  </property>

  <property>
    <name>dfs.namenode.secondary.http-address</name>
    <value>HBase02:9868</value>
  </property>

</configuration>
EOF
```

## /opt/hadoop-3.2.2/etc/hadoop/workers
```xml
cat >> /opt/hadoop-3.2.2/etc/hadoop/workers << EOF

HBase02
HBase03

EOF
```

## /opt/hadoop-3.2.2/etc/hadoop/mapred-site.xml
```xml
cat >> /opt/hadoop-3.2.2/etc/hadoop/mapred-site.xml << EOF

<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>

EOF
```

## /opt/hadoop-3.2.2/etc/hadoop/yarn-site.xml
```xml
cat >> /opt/hadoop-3.2.2/etc/hadoop/yarn-site.xml << EOF

<configuration>
   <property>
           <name>yarn.resourcenodemanager.hostname</name>
           <value>HBase01</value>
   </property>

   <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
   </property>
</configuration>

EOF
```

##
