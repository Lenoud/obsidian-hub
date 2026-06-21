# mariadb主slave

mariadb主slave相关技术笔记。

```bash
#两个节点关闭防火墙
systemctl disable firewalld
systemctl stop firewalld
setenforce 0
vi /etc/selinux/conf
#修改参数
SELINUX=disabled
```

配置yum源

```ini
cat local.repo << EOF
[mariadb]
name=mysql
baseurl=file:///opt/mariadb-repo
gpgcheck=0
enabled=1

[centos]
name=repo
baseurl=file:///mnt
gpgcheck=0
enabled=1

EOF
```

#两个节点安装数据库
yum -y install  mariadb mariadb-server

#启动数据库设置开机自启
systemctl enable mariadb
systemctl start mariadb

#初始化数据库密码
mysql_secure_installation

#注意有一个地方需要设置no

#Disallow root login remotely? NO

---

#主节点配置文件
vim /etc/my.cnf.d/server.cnf

[mysqld]
log_bin=mysql-bin
binlog_ignore_db=mysql
server_id=254  

```text
crudini --set /etc/my.cnf.d/server.cnf mysqld log_bin mysql-bin
crudini --set /etc/my.cnf.d/server.cnf mysqld binlog_ignore_db mysql
crudini --set /etc/my.cnf.d/server.cnf mysqld server_id 254
 #每个节点不能相同  最好写ip号
```

重启数据库

systemctl restart mariadb-server

```text
mysql -uroot -p000000 -e "grant all privileges  on . to root@'%' identified by '000000';"
mysql -uroot -p000000 -e "grant replication slave on . to 'user'@'mysql2' identified by '000000';"

```

##从节点配置文件
vim /etc/my.cnf.d/server.cnf

```text
crudini --set /etc/my.cnf.d/server.cnf mysqld log_bin mysql-bin
crudini --set /etc/my.cnf.d/server.cnf mysqld binlog_ignore_db mysql
crudini --set /etc/my.cnf.d/server.cnf mysqld server_id 253

```

重启数据库

systemctl restart mariadb-server

登录数据库mysql -uroot -p
配置从节点数据库信息
master_host为主节点主机名mysql1，master_user为上一步中创建的用户user

change master to master_host='mysql1',master_user='user',master_password='000000';

开启从节点服务
start slave;
show slave status\G

可以看到Slave_IO_Running和Slave_SQL_Running的状态都是Yes，配置数据库主从集群成功

```text
mysql -uroot -p000000 -e "change master to master_host='mysql1',master_user='user',master_password='000000';"
mysql -uroot -p000000 -e "start slave;"
mysql -uroot -p000000 -e "show slave status\G"
```
