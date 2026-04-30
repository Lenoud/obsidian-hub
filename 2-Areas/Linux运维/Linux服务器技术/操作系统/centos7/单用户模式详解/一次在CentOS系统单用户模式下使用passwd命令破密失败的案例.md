# 一次在CentOS系统单用户模式下使用passwd命令破密失败的案例

某次遇到需要进入系统的单用户模式进行破密操作，结果却显示如下：

![[1724033091959-970242bb-1619-4215-aed6-c0ee8afa2863.png]]

根据提示：Permission denied（缺少权限）

此时查看/usr/bin/passwd 权限：

![[1724033091969-e432e58f-069a-4f06-87e4-9e804ddb82ca.png]]

正常情况下的权限应该是如下：

```bash
[root@web ~]# ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 27856 Aug  9 09:39 /usr/bin/passwd
[root@web ~]#
```

发现权限异常，需要修复命令的权限：

```bash
chown root:root /usr/bin/passwd

chmod u=rwx,go=rx,u+s /usr/bin/passwd
```

再次出现错误提示：

![[1724033091904-60ae2d49-b23f-4c00-85c2-fd3b0eaddb69.png]]

### 原因&解决方法：
上面我们执行的chmod命令，其底层实现是chattr命令，用此命的功能更为强大，甚至可以锁定文件，即使root用户也操作不了此文件。

chattr是用来更改文件属性，lsattr可用来查看文件的属性，执行命令lsattr /webapps/.usr.ini便可以看到当前文件的属性；

可以发现当前文件有个i属性，查阅命令帮助文档可以看到有i属性的文件是不能修改的，更不可被删除，即使是root用户也不可。

既然知道了文件不能删除的原因是加了i属性，所以相应的解决方案就是把文件的i属性去除，然后再删除。

lsattr查看相关命令和文件的文件属性：

![[1724033091898-c2dc5240-cbcc-4d8b-8696-5ab90124b4bc.png]]

chattr -i 去除锁定的文件属性

![[1724033091958-21f42662-0ccf-462d-bc07-653d6df19f1b.png]]

尝试再次修改权限，无报错说明执行成功，如有错误需要检查之前的操作是否有遗漏：

chown root:root /usr/bin/passwd

chmod u=rwx,go=rx,u+s /usr/bin/passwd

![[1724033092293-1ef19669-cd48-4305-bea7-3075174690bb.png]]

再次尝试破解密码，执行成功，如有错误需要检查之前的操作是否有遗漏：

echo “123456” | passwd --stdin root     //修改密码

touch /.autorelabel                                         //让SELINUX生效，这一步一定不能少，不然不能重启

![[1724033092319-01d45425-b3a3-4cc2-b35d-5533cdd23b44.png]]

重启，输入破解后的密码可以正常进入系统：

exit

reboot

![[1724033092476-fe5d6a4d-1212-4bf2-ab01-e3f0f81fce2e.png]]
