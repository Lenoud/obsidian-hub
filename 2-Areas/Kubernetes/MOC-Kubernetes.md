# MOC - Kubernetes

Kubernetes 容器编排平台的学习笔记，涵盖基础概念、集群搭建、服务部署、Istio 服务网格、KubeVirt 虚拟化、dry-run 干跑测试以及 Go 语言开发 K8s API 等内容。

## dry-run
- [[1.25  sa不自动生成Secret，手动创建]]
- [[role和rolebinding的创建]]
- [[创建secret]]
- [[创建svc网络或者绑定svc网络]]
- [[创建集群角色绑定管理员]]

## Istio
- [[6.金丝雀（重要！！！考前必看）]]
- [[创建网关bookinfo-gateway]]
- [[基于Kubernetes + Istio实现灰度发布]]
- [[基于Kubernetes + Istio实现灰度发布(解释版)]]

## KubeVirt
- [[7.kubevirt虚拟化]]
- [[featureGates启用功能模块]]
- [[KubeVirt]]
- [[virtctl]]
- [[创建一个vm容器镜像]]
- [[datavolume]]
- [[存储]]
- [[启动一个openwrt虚拟机]]

## 服务部署
- [[Helm 安装 Longhorn 笔记（数据持久化解决方案）]]
- [[k8s部署deployment形式的wordpress]]
- [[Prometheus-k8s]]
- [[服务部署]]

## 基础概念
- [[coredns实现pod间的域名解析]]
- [[deployment运维]]
- [[helm]]
- [[k8s-1.18.0-kubeadm搭建]]
- [[k8s中Pod之间通信的概念]]
- [[k8s中不同名称空间的pod互访]]
- [[kubectl运维命令]]
- [[Kubernetes(K8S）所有核心服务服务的搭建]]
- [[Kubernetes搭建]]
- [[MiniKube部署]]
- [[pod运维]]
- [[pv回收策略]]
- [[存储的运维(pv&pvc&sc)]]
- [[分配一个伪终端]]
- [[环境变量配置]]
- [[集群角色绑定]]
- [[集群权限]]
- [[节点选择器&&节点亲和性]]
- [[就绪检测&&存活检测]]
- [[配置其它主机可以使用kubectl管理k8s]]
- [[日志采集]]
- [[设置工作目录]]
- [[使pod拥有真正的root权限（报错Failed to get D-Bus connection_ Operation not permitted）]]
- [[网络服务svc]]
- [[污点]]
- [[证书]]
- [[资源限制]]
- [[资源限制qosClass三种类型]]

## 开发
- [[apps_v1]]
- [[操作v1核心api]]
- [[导入kubeconfig.yaml]]
- [[使用utils函数创建yaml资源]]

## 相关领域
- [[MOC-Docker]] — K8s 底层容器运行时
- [[MOC-服务部署]] — Prometheus 等服务的 K8s 部署
- [[MOC-CICD]] — 基于 K8s 的 CICD 流水线
