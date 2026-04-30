```bash
#!/bin/bash
echo -e "\e[33m您确定要继续执行这个操作吗？ (yes/no)\e[0m"
read -r control

if [[ "$control" == "no" ]]; then
    echo -e "\e[31m操作已取消。\e[0m"
    exit 1
fi
echo -e "\e[32m继续执行操作...\e[0m"

#修改配置文件
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

echo -e "\e[32m配置修改完毕\e[0m"
```
