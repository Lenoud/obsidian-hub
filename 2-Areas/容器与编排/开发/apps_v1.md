# 获取deployment

获取deployment相关技术笔记。
```python
from kubernetes import client,config

config.load_kube_config(config_file=r"/home/liubiao/APP/pycharm-data/python-master/kubernetes-api/kubeconfig.yaml")

apps_v1=client.AppsV1Api()

namespace="default"

dep_list=[]

dep_name=apps_v1.list_namespaced_deployment(namespace=namespace)
#dep_name=apps_v1.read_namespaced_deployment(namespace=namespace,name="nginx") #指定一个deployment

#遍历每一个deploy
for i in dep_name.items:
    #print(i.metadata.name)
    name=i.metadata.name
    dep_list.append(i.metadata.name)
print(dep_list)
```

# 创建deployment
```python
from kubernetes import client, config
from kubernetes.client.models import (
    V1Deployment, V1ObjectMeta, V1DeploymentSpec, V1PodTemplateSpec,
    V1Container, V1LabelSelector, V1PodSpec
)
import time

# 配置与Kubernetes集群的连接
config.load_kube_config(config_file=r"/home/liubiao/APP/pycharm-data/python-master/kubernetes-api/kubeconfig.yaml")

# 创建API客户端
api_instance = client.AppsV1Api()

# 定义Deployment的元数据
metadata = V1ObjectMeta(name="my-nginx-deploy")

# 定义Pod模板规格
pod_template_spec = V1PodTemplateSpec(
    metadata=V1ObjectMeta(labels={"app": "nginx"}),
    spec=V1PodSpec(
        containers=[V1Container(name="nginx-container", image="nginx:latest",image_pull_policy="IfNotPresent")]
    )
)

# 定义Deployment规格
deployment_spec = V1DeploymentSpec(
    replicas=3,
    template=pod_template_spec,
    selector=V1LabelSelector(match_labels={"app": "nginx"})
)

# 定义Deployment
deployment = V1Deployment(
    metadata=metadata,
    spec=deployment_spec
)

api_instance.delete_namespaced_deployment(name="my-nginx-deploy",namespace="default")

time.sleep(3)

# 创建Deployment
apply=api_instance.create_namespaced_deployment(
    namespace="default",
    body=deployment
)
```
