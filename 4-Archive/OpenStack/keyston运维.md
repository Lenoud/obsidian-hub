# keyston运维

keyston运维相关技术笔记。

检测demo用户在不在test组里

[root@controller ~]# openstack group contains user test demo

demo not in group test

demo加入test组

[root@controller ~]# openstack group add  user test demo

[root@controller ~]# openstack group contains user test demo

demo in group test

[root@controller ~]# openstack role list

[root@controller ~]# openstack role show admin

[root@controller ~]# openstack  role assignment list

domain域

[root@controller ~]# openstack domain list

创建example域

[root@controller ~]# openstack domain create example

创建项目 并加入example域

[root@controller ~]# openstack project create --domain example myproject

[root@controller ~]# openstack user create user3 --password 000000 --domain demo --project myproject

给user1赋予普通用户权限

[root@controller ~]# openstack role add --user user1 --project demo user

给user3赋予普通用户权限

[root@controller ~]# openstack role add --user user3 --project demo user

[root@controller ~]# openstack role add --group test  --project demo user

给user1赋予admin权限

[root@controller ~]# openstack role add --project admin --user user1 admin

删除user1的admin权限

[root@controller ~]# openstack role remove --user user1 --project admin admin
