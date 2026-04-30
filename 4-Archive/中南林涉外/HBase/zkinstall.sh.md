## node1
```bash
#!/bin/bash
echo -e "\e[33m您确定要继续执行zkinstall.sh_node1这个操作吗？ (yes/no)\e[0m"
read -r control

if [[ "$control" == "no" ]]; then
    echo -e "\e[31m操作已取消。\e[0m"
    exit 1
fi
echo -e "\e[32m继续执行操作...\e[0m"

# 确保脚本以 root 用户权限运行
if [ "$(id -u)" -ne "0" ]; then
  echo "请以 root 用户运行此脚本"
  exit 1
fi

mkdir -p /opt/data/zookeeper/zkdata
echo 1 > /opt/data/zookeeper/zkdata/myid

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

zkServer.sh  start
```

## node2
```bash
#!/bin/bash
echo -e "\e[33m您确定要继续执行zkinstall.sh_node2这个操作吗？ (yes/no)\e[0m"
read -r control

if [[ "$control" == "no" ]]; then
    echo -e "\e[31m操作已取消。\e[0m"
    exit 1
fi
echo -e "\e[32m继续执行操作...\e[0m"

# 确保脚本以 root 用户权限运行
if [ "$(id -u)" -ne "0" ]; then
  echo "请以 root 用户运行此脚本"
  exit 1
fi

mkdir -p /opt/data/zookeeper/zkdata
echo 2 > /opt/data/zookeeper/zkdata/myid

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

zkServer.sh  start
```

## node3
```bash
#!/bin/bash
echo -e "\e[33m您确定要继续执行zkinstall.sh_node3这个操作吗？ (yes/no)\e[0m"
read -r control

if [[ "$control" == "no" ]]; then
    echo -e "\e[31m操作已取消。\e[0m"
    exit 1
fi
echo -e "\e[32m继续执行操作...\e[0m"

# 确保脚本以 root 用户权限运行
if [ "$(id -u)" -ne "0" ]; then
  echo "请以 root 用户运行此脚本"
  exit 1
fi

mkdir -p /opt/data/zookeeper/zkdata
echo 3 > /opt/data/zookeeper/zkdata/myid

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

zkServer.sh  start
```

## start
```bash
zkServer.sh  start
zkServer.sh start-foreground

zkServer.sh  status
```
