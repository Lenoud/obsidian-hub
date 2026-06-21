# heat编排

heat编排相关技术笔记。

openstack stack create -t create_container.yaml test-swift

```yaml
#创建一个容器
heat_template_version: 2014-10-16
resources:
  user:
    type: OS::Swift::Container
    properties:
      name: heat-swift
```

创建名为Heat-Network网络，选择不共享；创建子网名为Heat-Subnet，子网网段设置为10.20.2.0/24，开启DHCP服务，地址池为10.20.2.20-10.20.2.100。

```yaml
#创建网络和子网
heat_template_version: 2018-08-31
description: "this is a network"
resources:
  Network:
    type: OS::Neutron::Net
    properties:
      admin_state_up: true
      name: test-net
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

```yaml
#创建一个net
heat_template_version: 2018-08-31
description: "this is a network"
resources:
  Network:
    type: OS::Neutron::Net
    properties:
      admin_state_up: true
      name: Heat-Network
      shared: false
```

```yaml
#创建一个subnet
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
#创建一个flavor
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
#创建一台云主机
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
