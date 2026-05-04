# K8s 平台优化 — 修改 Pod 数量限制

Kubernetes 默认每个节点只能启动 110 个 Pod，修改配置将限制提升为 200。

```bash
[root@k8s-master-node1 ~]# cat /etc/sysconfig/kubelet
KUBELET_EXTRA_ARGS=--max-pods=200

#加上--max=pods=200
```

# 修改NodePort端口范围
Kubernetes以NodePort方式暴露服务默认的端口范围为30000-32767，请将NodePort的端口范围修改为1024-65535。

```yaml
vim /etc/kubernetes/manifests/kube-apiserver.yaml
spec:
  containers:
  - command:
    - --service-node-port-range=1024-65535
```
