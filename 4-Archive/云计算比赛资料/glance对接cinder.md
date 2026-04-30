# glance对接cinder

glance对接cinder相关技术笔记。



```shell

```

[root@controller ~]# vi /etc/glance/glance-api.conf

show_multiple_locations = true

[glance_store]

#stores = file,http

#demo_store = file

#filesystem_store_datadir = /var/lib/glance/images/

stores = cinder

default_store=cinder

[root@controller ~]# systemctl restart openstack-glance*

[root@controller ~]# vi /etc/cinder/cinder.conf

allowed_direct_url_schemes = cinder

image_upload_use_cinder_backend = true

image_upload_use_internal_tenant = true

#重启服务

[root@controller ~]# systemctl restart openstack-cinder*

测试

_openstack image create cirros < cirros-0.3.4-x86_64-disk.img _

_cinder list _

openstack image show cirros |grep cinder
