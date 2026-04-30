```bash
#!/bin/bash
cat >> /etc/profile << EOF

export JAVA_HOME=/opt/jdk1.8
export PATH=\$PATH:\$JAVA_HOME/bin

export HADOOP_HOME=/opt/hadoop-3.2.2
export PATH=\$PATH:\$HADOOP_HOME/bin:\$HADOOP_HOME/sbin

export ZK_HOME=/opt/zookeeper-3.7.0
export PATH=\$PATH:\$ZK_HOME/bin

EOF
```
