# nfs安装与配置

NFS 服务的安装与配置方法。

```bash
#1.所有节点下载 nfs
yum -y install nfs-utils

#2.server节点编辑配置文件
echo "/nfs/data/ *(insecure,rw,sync,no_root_squash)" > /etc/exports
#挂载到/nfs/data 目录并且允许所有用户访问

#3.设置服务开机自启并且立马启动
systemctl enable --now rpcbind
systemctl enable --now nfs-server

#4.生效配置&&查询配置的共享文件夹位置
exportfs -r  && exportfs

#5.client节点创建数据存放目录
mkdir -p  /clientDir

#6.client执行命令，挂载server节点的目录
mount -t nfs 192.168.100.10:/nfs/data   /clientDir

#查询主机是否有共享目录
showmount -e <ip>

```

###
