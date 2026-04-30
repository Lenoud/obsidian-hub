```bash
#!/bin/bash
stop-hbase.sh

sleep 10

stop-dfs.sh

zkServer.sh  stop
ssh root@slave1 'source /etc/profile && zkServer.sh  stop'
ssh root@slave2 'source /etc/profile && zkServer.sh  stop'

```
