# openstack-nova运维

openstack-nova运维相关技术笔记。

```bash
openstack server image create <serverName> [--name ]
#创建一个快照
 openstack image save cirros-vm --file /root/cirros-vm.qcow2
#保存快照到本地

openstack server create --image test-cirros --flavor min --security-group AllOpen  --nic net-id=0581b6f5-ae68-41b3-ba58-5e4dfcc8afb3,v4-fixed-ip=10.10.10.50 test-cirros-image
#创建云主机指定ip地址

openstack security group rule create --ingress --protocol icmp testgroup --remote-ip 192.168.200.0/24
#安全组放行规则并指定IP网络段
openstack security group rule create --egress --protocol tcp --dst-port 22
#指定放行端口

openstack volume create test_image-volume --image cirros --size 1
#创建一个云启动盘

openstack quota set --instances 10 demo
#将demo项目的实例上限调整为10个
```
