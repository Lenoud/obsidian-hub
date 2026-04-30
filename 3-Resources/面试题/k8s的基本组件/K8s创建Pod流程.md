**K8s创建Pod流程**

![[1747100443411-372d3244-849c-498b-8f3c-10d4e132b951.png]]

**Pod是Kubernetes中最基本的部署调度单元，可以包含container，逻辑上表示某种应用的一个实例。例如一个web站点应用由前端、后端及数据库构建而成，这三个组件将运行在各自的容器中，那么我们可以创建包含三个container的pod。**

---
**创建pod流程：**

![[1747100464859-505819b4-60f8-45f5-b4b6-5c3a79291362.png]]

---
**master节点：kubectl -> kube-api -> kubelet -> CRI容器环境初始化**

---
**第一步：**

**客户端提交创建Pod的请求，可以通过调用API Server的Rest API接口，也可以通过kubectl命令行工具。如kubectl apply -f filename.yaml(资源清单文件)**

---
**第二步：**

**apiserver接收到pod创建请求后，会将yaml中的属性信息(metadata)写入etcd。**

---
**第三步：**

**apiserver触发watch机制准备创建pod，信息转发给调度器scheduler，调度器使用调度算法选择node，调度器将node信息给apiserver，apiserver将绑定的node信息写入etcd**

---
**调度器用一组规则过滤掉不符合要求的主机。比如Pod指定了所需要的资源量，那么可用资源比Pod需要的资源量少的主机会被过滤掉。**

---
**scheduler 查看 k8s api ，类似于通知机制。
首先判断：pod.spec.Node == null?
若为null，表示这个Pod请求是新来的，需要创建；因此先进行调度计算，找到最“闲”的node。
然后将信息在etcd数据库中更新分配结果：pod.spec.Node = nodeA (设置一个具体的节点)
**

**ps:同样上述操作的各种信息也要写到etcd数据库中中**

---
**第四步：**

**apiserver又通过watch机制，调用kubelet，指定pod信息，调用Docker API创建并启动pod内的容器。**

---
**第五步：**

**创建完成之后反馈给kubelet, kubelet又将pod的状态信息给apiserver,
apiserver又将pod的状态信息写入etcd。**
