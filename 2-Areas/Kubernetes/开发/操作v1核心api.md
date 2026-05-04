# 列出pod

列出pod相关技术笔记。
```python
from kubernetes import client, config, utils

# Configs可以直接在Configuration类中设置，也可以使用helper工具
config.load_kube_config(config_file=r"/home/liubiao/APP/pycharm-data/python-master/kubernetes-api/kubeconfig.yaml")
k8s_client = client.ApiClient()

#导入核心v1Api资源
v1 = client.CoreV1Api()

#列出默认名称空间的第一个pod
pods = v1.list_namespaced_pod(namespace="default")
print(pods.items[0].metadata.name)

#遍历每一个pod
pod_N_list = []
for podN in pods.items:
    # print(podN.metadata.name)
    pod_N_list.append(podN.metadata.name)
print(pod_N_list)
```

# 删除pod
```python
from kubernetes import client, config

# Configs可以直接在Configuration类中设置，也可以使用helper工具
config.load_kube_config(config_file=r"/home/liubiao/APP/pycharm-data/python-master/kubernetes-api/kubeconfig.yaml")

v1 = client.CoreV1Api()

#删除pod
delete = v1.delete_namespaced_pod(name="lifecycle-demo1",namespace="default")
print(delete)
```

# 创建pod
```python
from kubernetes import client, config
from kubernetes.client.models import V1Pod, V1ObjectMeta, V1Container
import time

# 配置与Kubernetes集群的连接
config.load_kube_config(config_file=r"/home/liubiao/APP/pycharm-data/python-master/kubernetes-api/kubeconfig.yaml")

# 创建API客户端
api_instance = client.CoreV1Api()

pod_name = input("请输入pod名称：")

# 定义Pod的元数据
metadata = V1ObjectMeta(name=pod_name, labels={"app": "nginx"})

# 定义容器规格
container = V1Container(name="nginx", image="nginx", image_pull_policy="IfNotPresent")

# 定义Pod规格
spec = V1Pod(
    metadata=metadata,
    spec={"containers": [container]}
)

# 删除同名pod
try:
    api_instance.delete_namespaced_pod(namespace="default", name=pod_name)
    print("已删除pod,开始创建")
    time.sleep(3)
except:
    print("没有同名pod,开始创建")
# 创建Pod
api_instance.create_namespaced_pod(namespace="default", body=spec)

# 列出pod
pods = api_instance.list_namespaced_pod(namespace="default")
pod_N_list = []
for podN in pods.items:
    # 遍历podname写入列表
    pod_N_list.append(podN.metadata.name)
print(pod_N_list)

```
