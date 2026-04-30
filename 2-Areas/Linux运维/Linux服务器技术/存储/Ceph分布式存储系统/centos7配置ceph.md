# centos7配置ceph

数据库配置与权限管理。

```bash
#全节点关闭防火墙配置好yum源后,添加hosts映射,并且配置免密登陆
echo >> /etc/hosts

#ceph1安装ceph-deploy
yum -y install ceph-deploy

#ceph1节点 创建配置目录
mkdir /etc/ceph/
cd /etc/ceph/

#创建一个ceph集群，在ceph1上用ceph-deploy使所有节点安装Ceph
ceph-deploy new ceph1
ceph-deploy install ceph1 ceph2 ceph3

#ceph1 创建第一个 Ceph monitor
ceph-deploy --overwrite-conf mon create-initial
ceph -s

#全节点创建目录
mkdir /opt/osd
chmod 777 /opt/osd

#创建并激活 OSD 节点
cd /etc/ceph/
ceph-deploy osd prepare ceph1:/opt/osd ceph2:/opt/osd ceph3:/opt/osd
ceph-deploy osd activate ceph1:/opt/osd ceph2:/opt/osd ceph3:/opt/osd
ceph -s

#开放权限给其他节点，进行灾备处理
ceph-deploy admin ceph{1,2,3}
```
