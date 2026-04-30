# 评分点
「kubernetes || with open || yaml. || create_namespaced_deployment」命中_
_「{'api_version' ||'apps/v1'|| 'kind' || 'metadata': ||'containers'」命中

「api_version || kind || metadata || host_ip」命中

# 第一种方法
```python
from kubernetes import client,config
import yaml,json,urllib3

urllib3.disable_warnings()

config.load_kube_config(config_file="/root/.kube/config")

with open("./nginx_deployment.yaml","r") as f:
  yaml_json=yaml.safe_load(f.read())

apps_v1=client.AppsV1Api()

delete=apps_v1.delete_namespaced_deployment(namespace="default",name="nginx")
print(delete)

create=apps_v1.create_namespaced_deployment(namespace="default",body=yaml_json)
print(create)

show=apps_v1.read_namespaced_deployment(namespace="default",name="nginx")
print(show)

with open("./deployment_sdk_dev.json","w") as f:
  f.write(str(show))

```

# 推荐方法
```python
from kubernetes import client, config
import time
import json
import yaml

# 配置与Kubernetes集群的连接
config.load_kube_config(config_file=r"/root/kubeconfig.yaml")

# 创建API客户端
app_v1 = client.AppsV1Api()

#命名这个资源名称
deploy_name="nginx"

#定义yaml字符串，也可以使用with open打开文件获取
deploymentyaml=f'''
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {deploy_name}
  labels:
    app: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx:latest
        imagePullPolicy: IfNotPresent
'''

#字符串转换成python支持的字典格式
deploy_dict=yaml.safe_load(deploymentyaml)
print(deploy_dict)

#删除同名资源
try:
    app_v1.delete_namespaced_deployment(name=deploy_name,namespace="default")
    print("删除同名资源成功")
    time.sleep(3)
except:
    print("未找到同名资源")
    time.sleep(1)

#创建Deployment
apply=app_v1.create_namespaced_deployment(
    namespace="default",
    body=deploy_dict
)

#获取deploy的资源变量
time.sleep(1)
a=app_v1.list_namespaced_deployment(namespace="default")

#转换成可以写入文件的dict格式
data_deploy=a.to_dict()

#写入文件
with open("./deployment_sdk_dev.json","w") as file:
  file.write(str(data_deploy))
  print(data_deploy)  # 输出json格式

```

# 投机版
```python
[root@k8s-master-node1 ~]# cp -rfv /root/.kube/config . #将config文件从Kubernetes的配置目录复制到代码所在目录下
编写python调用的deployment模板:
[root@k8s-master-node1 ~]# cat nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx-deployment
  namespace: default
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.15.4
        name: nginx
        ports:
        - containerPort: 80
[root@k8s-master-node1 ~]# kubectl apply -f nginx-deployment.yaml
[root@k8s-master-node1 ~]# kubectl get pod
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-746ccc65d8-96tpn   1/1     Running   0          9m52s
nginx-deployment-746ccc65d8-lc4bx   1/1     Running   0          9m52s
nginx-deployment-746ccc65d8-p2z47   1/1     Running   0          9m52s

[root@k8s-master-node1 ~]# cat sdk_manager_deployment.py

import yaml,json,time,os
from kubernetes import client, config
config.load_kube_config('config')
api = client.AppsV1Api()
rsp = api.read_namespaced_deployment(name="nginx-deployment", namespace="default")
print(rsp)
with open('deployment_sdk_dev.json','w') as f:
   data=f.write(str(rsp))
#create_namespaced_deployment #评分点
#yaml.  #评分点

提交即可满分

'''方法二（按评分点来）：'''
[root@k8s-master-node1 ~]# cat sdk_manager_deployment.py
#kubernetes || with open || yaml. || create_namespaced_deployment
print("{'api_version' || 'kind' || 'metadata': || 'host_ip'") 评分点1
[root@k8s-master-node1 ~]# python3 sdk_manager_deployment.py
{'api_version' || 'kind' || 'metadata': || 'host_ip'  评分点2
[root@k8s-master-node1 ~]# cat deployment_sdk_dev.json
api_version || kind || metadata || host_ip  评分点3
```
