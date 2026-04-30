# openstack-glance运维

openstack-glance运维相关技术笔记。

## 1.swift配置为glance后端存储
vim /etc/glance/glance-api.conf

[glance_store]

stores = glance.store.swift.Store

#   修改为

default_store = swift

#    修改为

swift_store_auth_address = [http://controller:5000/v2.0/](http://controller:5000/v2.0/)

#   添加  #controllerIP

swift_store_container=chinaskill_glance

#上传镜像时容器的名称前缀带 chinaskill_glance_

swift_store_create_container_on_put = True

#      添加

swift_store_multi_tenant = True

#      添加

swift_store_admin_tenants = service

#       添加

随后重启glance服务
