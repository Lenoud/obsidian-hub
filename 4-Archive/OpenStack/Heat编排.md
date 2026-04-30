# Heat编排

Heat编排相关技术笔记。

创建名为Heat-Network网络，选择不共享；创建子网名为Heat-Subnet，子网网段设置为10.20.2.0/24，开启DHCP服务，地址池为10.20.2.20-10.20.2.100。完成后提交控制节点的用户名、密码和IP地址到答题框。（在提交信息前请准备好yaml模板执行的环境）

同时创建一个网络和一个子网

**[root@controller heat]# cat create_net.yaml**

```yaml
heat_template_version: 2018-08-31
description: "this is a network"
resources:
  Network:
    type: OS::Neutron::Net
    properties:
      admin_state_up: true
      name: Heat-Network
      shared: false
  Subnet:
    type: OS::Neutron::Subnet
    properties:
      allocation_pools:
      - end: 10.20.2.100
        start: 10.20.2.20
      cidr: 10.20.2.0/24
      enable_dhcp: true
      gateway_ip: 10.20.2.1
      ip_version: 4
      name: Heat-Subnet
      network_id: {get_resource: "Network"}
```

**create_subnet.yaml**

```yaml
heat_template_version: 2018-08-31
description: "this is a heat subnet"
resources:
  Subnet:
    type: OS::Neutron::Subnet
    properties:
      allocation_pools:
      - end: 10.20.2.100
        start: 10.20.2.20
      cidr: 10.20.2.0/24
      enable_dhcp: true
      gateway_ip: 10.20.2.1
      ip_version: 4
      name: Heat-Subnet
      network: Heat-Network
```

```yaml
heat_template_version: 2013-05-23
description: Test Template
resources:
  flavor1:
    type: OS::Nova::Flavor
    properties:
      name: "Test Heat server"
      flavorid: "1111"
      disk: 10
      ram: 1024
      vcpus: 2

```

```yaml
heat_template_version: 2013-05-23
description: Test Template
resources:
  server:
    type: OS::Nova::Server
    properties:
      name: "Test server"
#      securityGroup: e88f95b7-52b1-45b2-8b90-1149c1dc19be
      image: cirros
      flavor: Fmin
      networks:
      - network: intnet
```
