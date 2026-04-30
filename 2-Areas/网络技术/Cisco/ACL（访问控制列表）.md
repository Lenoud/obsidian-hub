# ACL（访问控制列表）

ACL（访问控制列表）相关技术笔记。

acl一般应用在离目的地尽可能近的接口下（源地址和目的地址）尽可能不影响到其它网段

```bash
#配置标准IP访问控制列表。

R2(config)#access-list  1 deny 172.16.2.0 0.0.0.255
#拒绝来自172.16.2.0网段的流量通过
R2(config)#access-list  1 permit 172.16.1.0 0.0.0.255
#允许来自172.16.1.0网段的流量通过

#把访问控制列表在接口下应用
R2(config)#interface fastEthernet 0/0
R2(config-if)#ip  access-group 1 out   //在接口下访问控制列表出栈流量调用
R2# show  ip  access-lists
```
