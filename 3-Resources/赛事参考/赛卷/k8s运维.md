## **【任务 4】容器云服务运维：Kubernetes 基于容器的运维**
### **【题目 1】**
使用Deployment方式将WordPress博客系统部署到Kubernetes集群default命名空间下，名称为wordpress，要求将wordpress应用和MySQL数据库部署到同一Pod中，使用环境变量设置数据库用户及密码均为wordpress，并新建数据库wordperss。

### **【题目 2】**
创建一个secret用于保存MySQL密码类型为Opaque

### **【题目 3】**
在Master搭建NFS服务将master的/data/kubernetes/目录共享出去，再创建一个名为nfs-storage的storageclass，随后使用nfs-storage创建一个名为wordpress-pvc的pvc大小为20Gi和一个名为mysql-pvc的pvc。

### **【题目 4】**
创建一个名为wordpress的deployment绑定上一题创建的wordpress-pvc存储卷，随后创建一个名为mysql的deployment绑定上一题创建的mysql-pvc存储卷mysql密码采用使用上一题创建的secret导入并创建名为wordpress的用户和名为wordpress的数据库，wordpress的mysql用户和密码均为wordpress

### **【题目 5】**
创建一个用户此用户只能访问default命名空间的资源，使用2023@chinaskill加密

### **【题目 6】**
helm部署mysql主从

### **【题目 7】**
创建一个名为 reviews 路由，要求来自名为 Jason 的用户的所有流量将被路由到服务 reviews:v2。

完成后提交 master 节点的用户名、密码和 IP 到答题框。

### **【题目 8】**
使用cirros-0.3.4-x86_64-disk.qcow2转换为kubevirt镜像，随后创建一个名为cirros的vmi虚拟机。
