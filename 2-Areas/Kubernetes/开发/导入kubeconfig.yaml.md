# 导入 kubeconfig.yaml

导入 kubeconfig.yaml相关技术笔记。

使用 Python Kubernetes SDK 通过 kubeconfig 文件连接 Kubernetes 集群。

```python
from kubernetes import client, config , utils

# Configs可以直接在Configuration类中设置，也可以使用helper工具
config.load_kube_config(config_file=r"/home/liubiao/APP/pycharm-data/python-master/kubernetes-api/kubeconfig.yaml")
```
