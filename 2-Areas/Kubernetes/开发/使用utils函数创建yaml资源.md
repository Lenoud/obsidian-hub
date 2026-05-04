# 使用 utils 函数创建 YAML 资源

使用 utils 函数创建 YAML 资源相关技术笔记。

使用 Python Kubernetes SDK 的 `utils.create_from_yaml` 函数直接从 YAML 文件创建 K8s 资源。

```python
from kubernetes import client, config, utils

# Configs可以直接在Configuration类中设置，也可以使用helper工具
config.load_kube_config(config_file=r"/home/liubiao/APP/pycharm-data/python-master/kubernetes-api/kubeconfig.yaml")
k8s_client = client.ApiClient()

#使用yaml创建
apply = utils.create_from_yaml(yaml_file=r"/home/liubiao/APP/pycharm-data/python-master/kubernetes-api/yamlfile/nginx.yaml", k8s_client=k8s_client)
print(apply)
```
