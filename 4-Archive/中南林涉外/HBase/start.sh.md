```bash
#master节点初始化hdfs
hdfs namenode -format

```

```bash

#hadoop集群启动
echo -e "\e[32mhadoop集群启动\e[0m"
start-dfs.sh
#start-yarn.sh

#zookeeper启动
echo -e "\e[32mzookeeper启动\e[0m"
zkServer.sh  start
ssh root@Hbase02 'source /etc/profile && zkServer.sh  start'
ssh root@Hbase03 'source /etc/profile && zkServer.sh  start'

sleep 10
#hbase集群启动
echo -e "\e[32mhbase集群启动\e[0m"
start-hbase.sh
```
