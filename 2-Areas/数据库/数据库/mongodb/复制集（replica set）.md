# MongoDB 复制集（Replica Set）

MongoDB 复制集常用命令参考，用于管理主从复制和高可用部署。

## 常用命令

| 命令 | 描述 |
| --- | --- |
| `rs.initiate()` | 初始化复制集 |
| `rs.add()` | 添加成员 |
| `rs.remove()` | 移除成员 |
| `rs.conf()` | 查看配置信息 |
| `rs.status()` | 查看状态信息 |
| `rs.isMaster()` | 检查是否为主节点 |
| `rs.secondaryOk()` | 允许从节点读操作 |
| `rs.stepDown()` | 主节点下台触发选举 |
| `rs.reconfig()` | 修改配置 |
| `rs.freeze()` | 暂停同步和选举 |
| `rs.unfreeze()` | 恢复同步和选举 |

## 高级命令

| 命令 | 描述 |
| --- | --- |
| `rs.initiate(config)` | 使用自定义配置初始化 |
| `rs.addArb(hostport)` | 添加投票节点 |
| `rs.printReplicationInfo()` | 打印副本同步信息 |
| `rs.printSlaveReplicationInfo()` | 打印从节点同步信息 |
