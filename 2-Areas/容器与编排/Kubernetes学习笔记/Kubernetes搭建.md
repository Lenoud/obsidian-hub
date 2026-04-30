# Kubernetes搭建

Kubernetes搭建相关技术笔记。

Kubernetes 容器编排相关笔记。

kubeeasy自动化搭建kubernetes

Github: [https://github.com/kongyu666/kubeeasy](https://github.com/kongyu666/kubeeasy)

/tmp/kubeeasy/manifests 部分yaml存放目录

**节点基本规划**

---
1.主机不能少于两台

IP	主机名	节点

10.24.2.10	Master	Kubernetes集群master节点、Harbor仓库节点

10.24.2.11	Worker	Kubernetes集群node节点

2. 基础环境配置

将提供的安装包chinaskills_cloud_paas_v2.0.iso下载至master节点/root目录，并解压到/opt目录：

[root@localhost ~]# curl -O [http://mirrors.douxuedu.com/competition/chinaskills_cloud_paas_v2.0.1.iso](http://mirrors.douxuedu.com/competition/chinaskills_cloud_paas_v2.0.1.iso)

[root@localhost ~]# mount -o loop chinaskills_cloud_paas_v2.0.1.iso /mnt/

[root@localhost ~]# cp -rfv /mnt/* /opt/

[root@localhost ~]# umount /mnt/

---

---

**基本依赖的安装**

**1.1 安装kubeeasy**

kubeeasy为Kubernetes集群专业部署工具，极大的简化了部署流程。其特性如下：

● 全自动化安装流程；

● 支持DNS识别集群；

● 支持自我修复：一切都在自动扩缩组中运行；

● 支持多种操作系统（如 Debian、Ubuntu 16.04、CentOS7、RHEL等）；

● 支持高可用。

在master节点安装kubeeasy工具：

[root@localhost ~]# mv /opt/kubeeasy /usr/bin/kubeeasy

**1.2 安装依赖包**

此步骤主要完成docker-ce、git、unzip、vim、wget等工具的安装。

在master节点执行以下命令完成依赖包的安装：

[root@localhost ~]# kubeeasy install depend \

--host 10.24.2.10,10.24.2.11 \

--user root \

--password 000000 \

--offline-file /opt/dependencies/base-rpms.tar.gz

参数解释如下：

● --host：所有主机节点IP，如：10.24.1.2-10.24.1.10，中间用“-”隔开，表示10.24.1.2到10.24.1.10范围内的所有IP。若IP地址不连续，则列出所有节点IP，用逗号隔开，如：10.24.1.2,10.24.1.7,10.24.1.9。

● --user：主机登录用户，默认为root。

● --password：主机登录密码，所有节点需保持密码一致。

● --offline-file：离线安装包路径。

可通过命令“tail -f /var/log/kubeinstall.log”查看安装详情或排查错误。

**1.3 配置SSH免密钥**

安装Kubernetes集群的时候，需要配置Kubernetes集群各节点间的免密登录，方便传输文件和通讯。

在master节点执行以下命令完成集群节点的连通性检测：

[root@localhost ~]# kubeeasy check ssh \

--host 10.24.2.10,10.24.2.11 \

--user root \

--password 000000

在master节点执行以下命令完成集群所有节点间的免密钥配置：

[root@localhost ~]# kubeeasy create ssh-keygen \

--master 10.24.2.10 \

--worker 10.24.2.11 \

--user root \

 --password 000000

–mater参数后跟master节点IP，–worker参数后跟所有worker节点IP。

---

---

## 环境配置完毕后即可开始安装kubernetes
本次安装的Kubernetes版本为v1.22.1。

在master节点执行以下命令部署Kubernetes集群：

[root@localhost ~]# kubeeasy install kubernetes \

--master 10.24.2.10 \

--worker 10.24.2.11 \

--user root \

--password 000000 \

--version 1.22.1 \

--offline-file /opt/kubernetes.tar.gz

部分参数解释如下：

–master：Master节点IP。

–worker：Node节点IP，如有多个Node节点用逗号隔开。

–version：Kubernetes版本，此处只能为1.22.1。

可通过命令“tail -f /var/log/kubeinstall.log”查看安装详情或排查错误。

部署完成后查看集群状态：

[root@k8s-master-node1 ~]# kubectl cluster-info

Kubernetes control plane is running at [https://apiserver.cluster.local:6443](https://apiserver.cluster.local:6443)

CoreDNS is running at [https://apiserver.cluster.local:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy](https://apiserver.cluster.local:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy)

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

查看节点负载情况：

[root@k8s-master-node1 ~]# kubectl top nodes --use-protocol-buffers

**以上是比赛包需要执行的操作**

## github-kubeesay
以下操作请先在Github下载kubeeasy-v1.3.2.sh脚本

```bash
chmod +x kubeeasy-v1.3.1
cp kubeeasy-v1.3.1 /usr/bin/

安装依赖包
kubeeasy install dependencies \
     --host 192.168.100.12,192.168.100.13 \
     --user root \
     --password 781023 \
     --offline-file centos-7-rpms.tar.gz

检测联通性
kubeeasy check ssh   --host 192.168.100.12,192.168.100.13   --user root --password 781023

配置免密
kubeeasy create ssh-keygen    --master 192.168.100.12 --worker 192.168.100.13   --user root --password 781023

升级内核
kubeeasy install upgrade-kernel   --host 192.168.100.12,192.168.100.13   --user root   --password 781023 --offline-file  kernel-rpms-v5.17.2.tar.gz

安装集群
kubeeasy install kubernetes   --master 192.168.100.12   --worker 192.168.100.13   --user root   --password 781023   --version 1.21.3   --pod-cidr 10.244.0.0/16 --offline-file ./kubeeasy-v1.3.1.tar.gz
```

---

安装高可用k8s集群（master节点个数必须大于等于3）

kubeeasy install kubernetes \

   --master 192.168.1.201,192.168.1.202,192.168.1.203 \

   --worker 192.168.1.204 \

   --user root \

   --password 000000 \

   --version 1.21.3 \

   --virtual-ip 192.168.1.250 \

   --pod-cidr 10.244.0.0/16 \

   --offline-file ./kubeeasy-v1.3.2.tar.gz

---

---

## 其它功能
### 重置K8S节点
使用kubeeasy将集群重置（意思就是删除所有相关软件，恢复一个纯净的系统）

1. 重置正常的K8S集群（会重置整个集群）

kubeeasy reset \

   --user root \

   --password 000000

1. 强制重置指定的节点（如果节点不正常可以选择强制重置节点）

kubeeasy reset --force \

   --master 10.24.2.10 \

   --worker 10.24.2.11 \

   --user root \

   --password 000000

### 增加K8S节点
使用kubeeasy将新的节点加入K8S集群中

1. 离线增加K8S节点

增加master节点只适用于高可用集群的模式

从普通的K8S集群转换为高可用的K8S集群点击 [这里](https://github.com/kongyu666/kubeeasy/blob/main/ConvertToHA.md)

# 需要先安装依赖包

kubeeasy install depend \

   --host 192.168.1.204,192.168.1.205 \

   --user root \

   --password 000000 \

  --offline-file ./centos-7-rpms.tar.gz

# 加入K8S集群

kubeeasy add \

   --worker 192.168.1.204,192.168.1.205 \

   --user root \

   --password 000000 \

   --offline-file ./kubeeasy-v1.3.2.tar.gz

### 删除K8S节点
使用kubeeasy将节点从K8S集群中删除

1. 删除K8S节点

删除操作会重置节点，包括删除K8S服务、docker数据等清空操作。

kubeeasy delete   --worker 192.168.1.202,192.168.1.203   --user root    --password 000000

### 移除K8S节点
使用kubeeasy将节点从K8S集群中移除

1. 移除K8S节点

移除操作会不会重置节点，只是从K8S集群中移除，并不会删除docker的数据。

kubeeasy remove    --worker 192.168.1.202,192.168.1.203   --user root    --password 000000
