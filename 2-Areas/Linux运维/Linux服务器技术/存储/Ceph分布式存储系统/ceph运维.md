# ceph运维

Ceph 分布式存储系统的日常运维命令。

```shell
ceph status    # 检查 Ceph 的状态
ceph -w      # 观察集群的健康情况
ceph quorum_status --format json-pretty  # 检查 Ceph monitor 仲裁状态
ceph mon dump   # 导出 Ceph monitor 的信息
ceph df      # 检查集群使用状态
ceph mon stat   # 检查 Ceph Monitor 的状态
ceph osd stat   # 检查 Ceph OSD 的状态
ceph pg stat   # 检查 Ceph PG 的状态
ceph pg dump   # 列出 PG
ceph osd lspools # 列出 Ceph 存储池
ceph osd tree   # 检查 OSD 的 CRUSH
ceph auth list  # 列出集群的认证密钥
```
