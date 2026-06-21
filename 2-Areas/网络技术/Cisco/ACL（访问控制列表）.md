# ACL(访问控制列表)

Cisco ACL(Access Control List)基于源/目的 IP、端口、协议对流量做 permit/deny 控制。通常应用在**离目的地尽可能近的接口**,避免影响其它网段。

```text
# 配置标准 IP 访问控制列表

R2(config)#access-list  1 deny 172.16.2.0 0.0.0.255
#拒绝来自172.16.2.0网段的流量通过
R2(config)#access-list  1 permit 172.16.1.0 0.0.0.255
#允许来自172.16.1.0网段的流量通过

#把访问控制列表在接口下应用
R2(config)#interface fastEthernet 0/0
R2(config-if)#ip  access-group 1 out   //在接口下访问控制列表出栈流量调用
R2# show  ip  access-lists
```
