# 动态NAT配置

动态 NAT 从公网 IP 地址池中按需为内网 IP 分配映射,空闲时回收。本例演示 Cisco 路由器上的动态 NAT 配置。

地址规划如下

![[1686575602321-b60d8e1c-a9df-4130-b084-f3fa032ff3b3.png]]

```text
R1(config)#int f0/0
R1(config-if)#ip nat inside #内网接口
R1(config-if)#exit
R1(config)#int s0/0/0
R1(config-if)#ip nat outside #外网接口
R1(config)#ip route 0.0.0.0 0.0.0.0 s0/0/0   //内网出去的设置默认路由
R1(config)#access-list 2 permit 192.168.1.0 0.0.0.255   配置规则列表
R1(config)#ip nat pool dtnet 202.102.192.1 202.102.192.1 netmask 255.255.255.0  //创建地址
R1(config)#ip nat inside source list 2 pool dtnet
R1#show ip nat translations
Pro  Inside global     Inside local       Outside local      Outside global
tcp 202.102.192.3:1025 172.16.1.4:1025    200.200.200.2:80   200.200.200.2:80
```

配置完成后可以ping server可以连通

![[1686575462030-e98d789d-8499-79a6d012ba64.png]]

```text
R1(config)#int s0/0/0
R1(config-if)#ip nat outside  //指定outside接口
R1(config-if)#exit
R1(config)#int f0/0
R1(config-if)#ip nat inside  //指定inside接口
R1(config-if)#exit
R1(config)#access-list 10 permit 172.16.1.0 0.0.0.255 //通过ACL定义哪些子网能做NAT
R1(config)#ip nat pool pool123 202.102.192.2 202.102.192.8 netmask 255.255.255.0  //创建地址池
R1(config)#ip nat inside source list 10 pool pool123 //关联ALC和地址池
R1(config)#ip route 0.0.0.0 0.0.0.0 s0/0/0 //配置到达公网的默认路由
```

![[1686575796637-d2f8352b-5deb-4051-8ade-633078552554.png]]

![[1686894009433-84a74e79-5d0a-4bab-aea1-a43804bbc29d.png]]

![[1686575817469-36a2a625-3851-442e-8c9a-f4b312bbfb32.png]]
