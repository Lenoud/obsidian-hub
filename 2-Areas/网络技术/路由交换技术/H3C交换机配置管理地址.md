# H3C交换机配置管理地址

H3C交换机配置管理地址相关技术笔记。

数据库配置与权限管理。

```text
[sw-student14]vlan 999
[sw-student14-vlan999] port GigabitEthernet 1/0/24
[sw-student14-vlan999] quit
[sw-student14]interface vlan-interface 999
[sw-student14-Vlan-interface999]ip address 192.168.0.50 255.255.255.0
[sw-student14-Vlan-interface999]quit
[sw-student14]local-user admin
[sw-student14-luser-admin]service-type web
[sw-student14-luser-admin]authorization-attribute level 3
[sw-student14-luser-admin]password simple admin
```
