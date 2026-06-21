# 导入 kubeconfig(Python Kubernetes SDK)

使用 Python Kubernetes SDK 通过 kubeconfig 文件连接 Kubernetes 集群,后续可调用 API 管理各类资源。

## 安装 SDK

```bash
pip install kubernetes
```

## 通过 kubeconfig 连接

```python
from kubernetes import client, config

# 方式一:加载默认 kubeconfig(~/.kube/config)
config.load_kube_config()

# 方式二:指定 kubeconfig 路径
config.load_kube_config(config_file="/path/to/kubeconfig.yaml")

# 方式三:从字符串加载(适合远程拉取)
config.load_kube_config_from_dict(config_dict)

# 创建 API 客户端
v1 = client.CoreV1Api()
apps_v1 = client.AppsV1Api()

# 列出所有命名空间
for ns in v1.list_namespace().items:
    print(ns.metadata.name)

# 列出 default 命名空间的 Pod
pods = v1.list_namespaced_pod("default")
for pod in pods.items:
    print(pod.metadata.name, pod.status.phase)
```

## 在集群内运行(用 ServiceAccount)

如果代码运行在 K8s 集群内的 Pod 里,**不需要** kubeconfig,直接用 ServiceAccount:

```python
from kubernetes import client, config

# 自动加载当前 Pod 的 ServiceAccount token
config.load_incluster_config()

v1 = client.CoreV1Api()
```

需要在部署清单里指定 ServiceAccount 并绑定 RBAC:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-sa
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: my-sa-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: view        # 或自建 Role
subjects:
  - kind: ServiceAccount
    name: my-sa
    namespace: default
```

## 相关笔记
- [[操作v1核心api]] — Core API 操作
- [[apps_v1]] — Apps API(Deployment/StatefulSet)
- [[使用utils函数创建yaml资源]] — 通过 yaml 创建资源
- [[集群权限]] — RBAC
- [[MOC-Kubernetes]]

