# Python调用Kubernetes API运维

Python调用Kubernetes API运维相关技术笔记。
# 开发案例
2022年3月16日

## 案例描述
通过本模块课程学习，主要掌握以下内容：

（1）了解Kubernetes Python APIs库。

（2）了解Kubernetes Python SDK库。

（3）使用Kubernetes Python运维编写代码。

（4）掌握Kubernetes Pod、Service、Deployment、Job资源管理。

## 案例一、Python开发环境准备
任务目标：

搭建Kubernetes Python开发环境。

### 案例准备
K8S部署架构如图所示：

![[1686464853435-35d0e38b-8d59-4067-95b8-4257dab42b62.png]]

K8s内部服务架构如下：

![[1686464853935-932bb792-43c0-4f6d-8226-f6e27d1aa576.png]]

包括API Server、Scheduler、Controller、etcd等服务。其中外部调用都是通过API Server完成。

本案例采用单节点的Kubernetes环境，Master、Worker安装在一台机器里面。

节点规划，见表1-1.

表1-1节点规划

| **IP** | **主机名** | **节点** |
| :---: | :---: | :---: |
| 192.168.100.20 | Master/Worker | 单节点 |

### 案例实施
使用提供的python-3.6.8.tar.gz，解压并配置yum源，安装python3环境。查看Python版本，命令如下：

【说明：为转化docx到md文件，代码前后隐藏补md代码标签】

```bash

[root@ controller ~]# python3 --version

Python 3.6.8

```

安装Kubernetes·SDK Python模块

【说明：为转化docx到md文件，代码前后隐藏补md代码标签】

```bash

pip3 install kubernetes

```

## 案例二、通过Restful APIs编写Python运维案例
案例目标

（1）了解Kubernetes APIs。

接口说明：[https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/)

（2）了解Pod、Deployment、Service等资源管理代码Python编写方法。

### 实施准备
**1.接口说明**

Kubernetes 控制面的核心是API服务器。API服务器负责提供 HTTP API，以供用户、集群中的不同部分和集群外部组件相互通信。

Kubernetes API可以查询和操纵 Kubernetes API 中对象（例如：Pod、Namespace、ConfigMap 和 Event）的状态。

大部分操作都可以通过kubectl 命令行接口或 类似 kubeadm 这类命令行工具来执行，这些工具在背后也是调用API。不过，也可以使用REST调用来访问这些API。

具体的API说明参见：

[https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/)

2.kubernetes认证授权

[Kubernetes命名空间](https://www.cnblogs.com/saneri/p/13553979.html)（namespace）是Kubernetes提供的组织机制，用于给集群中的任何对象组进行分类、筛选和管理。每一个添加到Kubernetes集群的工作负载必须放在一个命名空间中，通过命名空间实现Kubernetes RBAC（基于角色的）访问控制。

因为kubernetes是高度模块化，客户端请求的时候首先需要进行认证，认证通过后再进行授权检查。当通过API访问时，API server 期望 Authorization header 中包含 Bearer token 的值。Bearer token 必须是一个字符串序列，只需使用 HTTP 的编码和引用功能就可以将其放入到 HTTP header 中。

Kubernetes默认情况下，新的集群上有三个命名空间：

（1）default：向集群中添加对象而不提供命名空间，这样它会被放入默认的命名空间中。在创建替代的命名空间之前，该命名空间会充当用户新添加资源的主要目的地，无法删除。

（2）kube-public：kube-public命名空间的目的是让所有具有或不具有身份验证的用户都能全局可读。

（3）kube-system：kube-system命名空间用于Kubernetes管理的Kubernetes组件，一般规则是，避免向该命名空间添加普通的工作负载。它一般由系统直接管理，因此具有相对宽松的策略。

3.查询认证相关信息

查找API网络地址：

```

[root@master opt]# kubectl cluster-info

Kubernetes master is running at [https://192.168.138.201:6443](https://192.168.138.201:6443)

KubeDNS is running at [https://192.168.138.201:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy](https://192.168.138.201:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy)

网络地址为：[https://192.168.138.201:6443](https://192.168.138.201:6443)

```

查询Token：

（1）使用kube-system命名空间创建服务账号“admin1”

*```

#kubectl create serviceaccount admin1 -n kube-system

```

（2）创建RBAC > 集群角色绑定

*```

#kubectl create clusterrolebinding admin1 --clusterrole=cluster-admin --serviceaccount=kube-system:admin1

```

（3）查询secrets与 Token

*```

[root@master ~]# kubectl get secrets  -n kube-system|grep admin

NAME    TYPE            DATA   AGE

admin1-token-bb6l4     kubernetes.io/service-account-token   3      147m

[root@master ~]# kubectl describe secrets -n kube-system admin1-token-bb6l4

Name:         admin1-token-bb6l4

Namespace:    kube-system

Labels:       <none>

Annotations:  kubernetes.io/service-account.name: admin1

              kubernetes.io/service-account.uid: bf16c94d-2e25-495d-b9dd-515570cd0dc7

Type:  kubernetes.io/service-account-token

Data

====

ca.crt:     1025 bytes

namespace:  11 bytes

token:      eyJhbGciOiJSUzI1Ni…VcdP0y0hZLas4gNA

```

### 案例实施
#### 1.Pod资源管理
（1）接口说明

Pod API说明网址：

[https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#pod-v1-core](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#pod-v1-core)

创建Pod：

HTTP 请求：

POST /api/v1/namespaces/{namespace}/pods

| 路径参数 | 描述 |
| --- | --- |
| namespace | 命名空间名称 |

Body参数

| **参数** | **描述** |
| --- | --- |
| **body** | Pod yaml |

其他接口类似：

| 创建 | POST /api/v1/namespaces/{namespace}/pods |
| --- | --- |
| 删除 | DELETE /api/v1/namespaces/{namespace}/pods/{name} |
| 查找 | GET /api/v1/namespaces/{namespace}/pods
GET /api/v1/namespaces/{namespace}/pods/{name} |
| 更新 | PUT /api/v1/namespaces/{namespace}/pods/{name} |

（2）代码实现

创建Pod使用本地yaml文件，创建nginx.yaml文件。

nginx.yaml文件内容如下：

```

apiVersion: v1

kind: Pod

metadata:

  name: pod-nginx

spec:

  containers:

  - name: nginx

    image: nginx:1.15.4

    ports:

    - containerPort: 8080

      protocol: TCP

```

创建api_pod_manager.py，进行Pod的增删查改操作。

```

# Copyright 2021~2022 The Cloud Computing support Teams of ChinaSkills.

from ipaddress import ip_address

import requests, json, time

import logging

import os,yaml

# -----------logger-----------

# get logger

logger = logging.getLogger(__name__)

# level

logger.setLevel(logging.DEBUG)

# format

format = logging.Formatter('%(asctime)s %(message)s')

# to console

stream_handler = logging.StreamHandler()

stream_handler.setFormatter(format)

logger.addHandler(stream_handler)

# -----------logger-----------

# 参考v1.23接口文档地址：

# [https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#pod-v1-core](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#pod-v1-core)

def get_api_server_token(api_server_token, node_url):

    #for request header

    bearer_token = "bearer " + api_server_token

    return bearer_token

class pod_manager():

    def __init__(self, node_url: str, bearer_token: str):

        self.node_url = node_url

        self.bearer_token = bearer_token

    # 创建pod

    def create_pod(self, yamlFile:str,namespace:str):

        headers = {

            "Authorization": self.bearer_token,

            "Content-Type": "application/json"

        }

        # 读取yaml文件，并转化为JSON数据

        with open(yamlFile, encoding="utf8") as f:

            body = json.dumps(yaml.safe_load(f))

        request_url = self.node_url + "/api/v1/namespaces/" + namespace +"/pods"

        result = json.loads(requests.post(request_url, data=body, headers=headers, verify=False).text)

        logger.debug(f"返回信息:{str(result)}")

        return result

    # 查询上面创建的pod

    def get_pod(self, pod_name: str):

        headers = {

            "Authorization": self.bearer_token

        }

        request_url = self.node_url + "/api/v1/namespaces/default/pods/" + pod_name

        result = json.loads(requests.get(request_url, headers=headers, verify=False).text)

        logger.debug(f"返回信息:{str(result)}")

        return result

    #更新Pod的镜像为nginx:1.16

    def update_pod(self, yamlFile:str, pod_name:str,namespace:str):

        headers = {

            "Authorization": self.bearer_token,

            "Content-Type": "application/strategic-merge-patch+json"

        }

        # 读取yaml文件，并转化为JSON数据

        with open(yamlFile, encoding="utf8") as f:

            body = json.dumps(yaml.safe_load(f))

        request_url = self.node_url + "/api/v1/namespaces/" + namespace + "/pods"+ pod_name

        result = json.loads(requests.patch(request_url, data=json.dumps(body), headers=headers, verify=False).text)

        logger.debug(f"返回信息:{str(result)}")

        return result

    #删除pod

    def delete_pod(self,pod_name: str):

        headers = {

            "Authorization": self.bearer_token

        }

        request_url = self.node_url + "/api/v1/namespaces/default/pods/" + pod_name

        result = json.loads(requests.delete(request_url, headers=headers, verify=False).text)

        logger.debug(f"返回信息:{str(result)}")

        return result

if __name__ == "__main__":

    # 使用自建token

    # 命令如下

    # kubectl create serviceaccount admin1 -n kube-system

    # kubectl create clusterrolebinding admin1 --clusterrole=cluster-admin --serviceaccount=kube-system:admin1

    # 查询名称admin1 - token - lwf5v

    # kubectl get secrets  -n kube-system

    # kubectl describe secrets -n kube-system admin1-token-lwf5v

    # 将获取到的token值放到api_server_token中去

    api_server_token = "eyJhbGciOiJSUzI1NiIsImtpZ…0hZLas4gNA"

    # 节点URL地址 192.168.138.201

    cluster_server_url = "https://192.168.138.201:6443"

    bearer_token = get_api_server_token(api_server_token, cluster_server_url)

    pod_m = pod_manager(cluster_server_url, bearer_token)

    # #创建Pod

    print("--------create pod with a yaml file ------------")

    pod_m.create_pod(yamlFile="nginx-pod.yaml",namespace="kube-system")

    name = "pod-nginx"

    #查找Pod

    print("--------get pod with name ------------")

    #pod_m.get_pod(name)

    #更新Pod

    print("--------update_pod pod with name ------------")

    #pod_m.update_pod(pod_name=name)

    # 删除Pod

    print("--------delete pod with name ------------")

    # pod_m.delete_pod(pod_name=name)

```

（3）代码验证

创建pod运行结果如下：

```

#python api_pod_manager.py

--------create pod with a yaml file ------------

lib\site-packages\urllib3\connectionpool.py:847: InsecureRequestWarning: Unverified HTTPS request is being made. Adding certificate verification is strongly advised. See: [https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings](https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings)

  InsecureRequestWarning)

2022-03-14 16:17:50,473 返回信息:{'kind': 'Pod', 'apiVersion': 'v1', 'metadata': {'name': 'pod-nginx', 'namespace': 'kube-system', 'selfLink': '/api/v1/namespaces/kube-system/pods/pod-nginx', 'uid': 'a47f810f-c544-4978-b9a3-2b0ce4e39037', 'resourceVersion': '77100', 'creationTimestamp': '2022-03-14T08:17:50Z', 'managedFields': [{'manager': 'python-requests', 'operation': 'Update', 'apiVersion': 'v1', 'time': '2022-03-14T08:17:50Z', 'fieldsType': 'FieldsV1', 'fieldsV1': {'f:spec': {'f:containers': {'k:{"name":"nginx"}': {'.': {}, 'f:image': {}, 'f:imagePullPolicy': {}, 'f:name': {}, 'f:ports': {'.': {}, 'k:{"containerPort":8080,"protocol":"TCP"}': {'.': {}, 'f:containerPort': {}, 'f:protocol': {}}}, 'f:resources': {}, 'f:terminationMessagePath': {}, 'f:terminationMessagePolicy': {}}}, 'f:dnsPolicy': {}, 'f:enableServiceLinks': {}, 'f:restartPolicy': {}, 'f:schedulerName': {}, 'f:securityContext': {}, 'f:terminationGracePeriodSeconds': {}}}}]}, 'spec': {'volumes': [{'name': 'default-token-6cm8t', 'secret': {'secretName': 'default-token-6cm8t', 'defaultMode': 420}}], 'containers': [{'name': 'nginx', 'image': 'nginx:1.15.4', 'ports': [{'containerPort': 8080, 'protocol': 'TCP'}], 'resources': {}, 'volumeMounts': [{'name': 'default-token-6cm8t', 'readOnly': True, 'mountPath': '/var/run/secrets/kubernetes.io/serviceaccount'}], 'terminationMessagePath': '/dev/termination-log', 'terminationMessagePolicy': 'File', 'imagePullPolicy': 'IfNotPresent'}], 'restartPolicy': 'Always', 'terminationGracePeriodSeconds': 30, 'dnsPolicy': 'ClusterFirst', 'serviceAccountName': 'default', 'serviceAccount': 'default', 'securityContext': {}, 'schedulerName': 'default-scheduler', 'tolerations': [{'key': 'node.kubernetes.io/not-ready', 'operator': 'Exists', 'effect': 'NoExecute', 'tolerationSeconds': 300}, {'key': 'node.kubernetes.io/unreachable', 'operator': 'Exists', 'effect': 'NoExecute', 'tolerationSeconds': 300}], 'priority': 0, 'enableServiceLinks': True}, 'status': {'phase': 'Pending', 'qosClass': 'BestEffort'}}

Process finished with exit code 0

```

可以通过命令行查询验证：

```

[root@master ~]# kubectl get pods -n kube-system

NAME                             READY   STATUS    RESTARTS   AGE

coredns-7ff77c879f-c74h9         1/1     Running   1          34d

coredns-7ff77c879f-ln5f9         1/1     Running   1          34d

etcd-master                      1/1     Running   1          34d

kube-apiserver-master            1/1     Running   1          34d

kube-controller-manager-master   1/1     Running   1          34d

kube-flannel-ds-nqhrq            1/1     Running   1          34d

kube-proxy-b9dmz                 1/1     Running   1          34d

kube-scheduler-master            1/1     Running   1          34d

pod-nginx                        1/1     Running   0          2m28s

```

（2）注释掉创建，进行查询测试

```

#python.exe api_pod_manager.py

--------get pod with name ------------

Python36\lib\site-packages\urllib3\connectionpool.py:847: InsecureRequestWarning: Unverified HTTPS request is being made. Adding certificate verification is strongly advised. See: [https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings](https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings)

  InsecureRequestWarning)

2022-03-14 16:33:38,381 返回信息:{'kind': 'Pod', 'apiVersion': 'v1', 'metadata': {'name': 'pod-nginx', 'namespace': 'kube-system', 'selfLink': '/api/v1/namespaces/kube-system/pods/pod-nginx', 'uid': 'a47f810f-c544-4978-b9a3-2b0ce4e39037', 'resourceVersion': '77121', 'creationTimestamp': '2022-03-14T08:17:50Z', 'managedFields': [{'manager': 'python-requests', 'operation': 'Update', 'apiVersion': 'v1', 'time': '2022-03-14T08:17:50Z', 'fieldsType': 'FieldsV1', 'fieldsV1': {'f:spec': {'f:containers': {'k:{"name":"nginx"}': …'image': 'nginx:1.15.4', 'imageID': 'docker-pullable://nginx@sha256:e8ab8d42e0c34c104ac60b43ba60b19af08e19a0e6d50396bdfd4cef0347ba83', 'containerID': 'docker://40aa6f1f7e0249cb1900cef615a1c87e5afab99b47c3f7989fee6c4b9e40ff14', 'started': True}], 'qosClass': 'BestEffort'}}

```

更新与删除同样操作进行测试。

#### 2. Deployment资源管理
（1）接口说明

Deployment API说明网址：

[https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#create-deployment-v1-apps](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#create-deployment-v1-apps)

创建Deployment：

HTTP 请求：

POST /api/v1/namespaces/{namespace}/deployments

| 路径参数 | 描述 |
| --- | --- |
| namespace | 命名空间名称 |

Body参数

| **参数** | **描述** |
| --- | --- |
| body | Deployments yaml |

其他接口类似：

| 创建 | POST /api/v1/namespaces/{namespace}/deployments |
| --- | --- |
| 删除 | DELETE /api/v1/namespaces/{namespace}/deployments/{name} |
| 查找 | GET /api/v1/namespaces/{namespace}/deployments
GET /api/v1/namespaces/{namespace}/deployments/{name} |
| 更新 | PUT /api/v1/namespaces/{namespace}/deployments/{name} |

（2）代码实现

创建nginx-deployment.yaml文件，用于创建内容如下：

```

apiVersion: apps/v1

kind: Deployment

metadata:

  name: nginx-deployment

  labels:

    app: nginx

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

      - name: nginx

        image: nginx:1.15.4

        ports:

        - containerPort: 80

```

再创建一个nginx-deployment-update.yaml，用于测试更新service。

```

spec:

  containers:

  - name: nginx

    image: nginx:1.16

```

创建api_deployment_manager.py，进行deployment的增删查改操作，代码如下：

```

import requests,time

import logging

import os,yaml,json

#-----------logger-----------

#get logger

logger = logging.getLogger(__name__)

# level

logger.setLevel(logging.DEBUG)

# format

format = logging.Formatter('%(asctime)s %(message)s')

# to console

stream_handler  = logging.StreamHandler()

stream_handler .setFormatter(format)

logger.addHandler(stream_handler )

#-----------logger-----------

def get_api_server_token(api_server_token, node_url):

      # Bearer token

    bearer_token = "bearer " + api_server_token

    return bearer_token

class api_deployment_manager():

    def __init__(self,node_url: str, bearer_token: str):

        self.node_url = node_url

        self.bearer_token = bearer_token

    #调用API创建deployment

    def create_deployment(self,yamlFile,namespace:str):

        headers = {

            "Authorization": self.bearer_token,

            "Content-Type": "application/json"

        }

        # 读取yaml文件，并转化为JSON数据

        with open(yamlFile, encoding="utf8") as f:

            body = json.dumps(yaml.safe_load(f))

        request_url = self.node_url + "/apis/apps/v1/namespaces/" + namespace + "/deployments"

        result = json.loads(requests.post(request_url, data=body, headers=headers, verify=False).text)

        logger.debug(f"返回信息:{str(result)}")

        return result

    #获取deployment，如果想指定获取某个deployment的信息，可以加上deployment_name,若不想则去掉。

    def get_deployment(self,deployment_name:str, namespace:str):

        headers = {

            "Authorization": self.bearer_token,

            "Content-Type": "application/json"

        }

        # GET /apis/apps/v1/namespaces/{namespace}/deployments

        request_url = self.node_url + "/apis/apps/v1/namespaces/" + namespace + "/deployments/" + deployment_name

        result = json.loads(requests.get(request_url, headers=headers, verify=False).text)

        logger.debug(f"返回信息:{str(result)}")

        return result

    #更新deployment镜像为1.16

    def update_deployment(self,deployment_name:str,yamlFile:str, namespace:str):

        headers = {

            "Authorization": self.bearer_token,

            "Content-Type": "application/strategic-merge-patch+json"

        }

        #body = {"spec": {"template": {"spec": {"containers": [{"name": "nginx", "image": "nginx:1.16"}]}}}}

        with open(yamlFile, encoding="utf8") as f:

            body = json.dumps(yaml.safe_load(f))

        request_url = self.node_url + "/apis/apps/v1/namespaces/" + namespace + "/deployments/" + deployment_name

        result = json.loads(requests.put(request_url, data=json.dumps(body), headers=headers, verify=False).text)

        logger.debug(f"返回信息:{str(result)}")

        return result

    #删除deployment

    def delete_deployment(self,deployment_name:str, namespace:str):

        headers = {

            "Authorization": self.bearer_token,

            "Content-Type": "application/strategic-merge-patch+json"

        }

        request_url = self.node_url + "/apis/apps/v1/namespaces/" + namespace + "/deployments/" + deployment_name

        result = json.loads(requests.delete(request_url, headers=headers, verify=False).text)

        logger.debug(f"返回信息:{str(result)}")

        return result

if __name__ == "__main__":

    # 将获取到的token值放到api_server_token中去

    api_server_token = "eyJhbGciOiJSUzI1NiIsImtpZCI6In…uS2FMb5KUjKpx_Ci_tCoTJHu1CqVKDu-LBEzboeIFosh4wTMGImDUOm0j_o9HvXA"

    # 节点URL地址 192.168.138.201

    cluster_server_url = "https://192.168.138.201:6443"

    bearer_token = get_api_server_token(api_server_token, cluster_server_url)

    dep_m = api_deployment_manager(cluster_server_url,bearer_token)

    deployment_name = "nginx-deployment"

    #获取deployment

    print("get deployment-------------------")

    result = dep_m.get_deployment(deployment_name, namespace="default")

    print(result)

    # 创建deployment

    # print("create deployment-------------------")

    result = dep_m.create_deployment(yamlFile="nginx-deployment.yaml", namespace="default")

    print(result)

    # 获取deployment

    print("get deployment-------------------")

    result = dep_m.get_deployment(deployment_name,namespace="default")

    print(result)

    # 更新deployment

    print("update deployment-------------------")

    dep_m.update_deployment(deployment_name=deployment_name,yamlFile="nginx-deployment-update.yaml", namespace="default")

    # # 删除deployment

    # print("delete deployment-------------------")

    # result = dep_m.delete_deployment(deployment_name=deployment_name,namespace="default")

# print(result)

```

（3）代码验证

Deployment资源管理执行结果为(部分结果)：

```

#python.exe  api_deployment_manager.py

get deployment-------------------

\site-packages\urllib3\connectionpool.py:847: InsecureRequestWarning: Unverified HTTPS request is being made. Adding certificate verification is strongly advised. See: [https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings](https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings)

  InsecureRequestWarning)

2022-03-15 13:28:06,518 返回信息:{'kind': 'Deployment', 'apiVersion': 'apps/v1', 'metadata': {'name': 'nginx-deployment', 'namespace': 'default', 'selfLink': '/apis/apps/v1/namespaces/default/deployments/nginx-deployment', 'uid': '29c68581-7480-4a34-b425-18d0245370a1', 'resourceVersion': '33677', 'generation': 1, 'creationTimestamp': '2022-03-15T03:28:22Z', 'labels': {'app': 'nginx'}, 'annotations': {'deployment.kubernetes.io/revision': '1'}, 'managedFields': [{'manager': 'python-requests', 'operation': 'Update',

{'name': 'nginx-deployment', 'group': 'apps', 'kind': 'deployments'}, 'code': 409}

get deployment-------------------

2022-03-15 13:28:06,552 返回信息:{'kind': 'Status', 'apiVersion': 'v1', 'metadata': {}, 'status': 'Failure', 'message': 'deployments.apps "nginx-deployment" already exists', 'reason': 'AlreadyExists', 'details': {'name': 'nginx-deployment', 'group': 'apps', 'kind': 'deployments'}, 'code': 409}

C:\Python\Python36\lib\site-packages\urllib3\connectionpool.py:847: InsecureRequestWarning: Unverified HTTPS request is being made. Adding certificate verification is strongly advised. See: [https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings](https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings)

  InsecureRequestWarning)

2022-03-15 13:28:06,570 返回信息:{'kind': 'Deployment', 'apiVersion': 'apps/v1', 'metadata': {'name': 'nginx-deployment', 'namespace': 'default', 'selfLink': '/apis/apps/v1/namespaces/default/deployments/nginx-deployment', 'uid': '29c68581-7480-4a34-b425-18d0245370a1', 'resourceVersion': '33677', 'generation': 1, 'creationTimestamp': '2022-03-15T03:28:22Z', 'labels': {'app': 'nginx'},

 {'type': 'Progressing', 'status': 'True', 'lastUpdateTime': '2022-03-15T03:59:58Z', 'lastTransitionTime': '2022-03-15T03:59:32Z', 'reason': 'NewReplicaSetAvailable', 'message': 'ReplicaSet "nginx-deployment-7c9f54d5f9" has successfully progressed.'}]}}

update deployment-------------------

C:\Python\Python36\lib\site-packages\urllib3\connectionpool.py:847: InsecureRequestWarning: Unverified HTTPS request is being made. Adding certificate verification is strongly advised. See: [https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings](https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings)

  InsecureRequestWarning)

2022-03-15 13:28:06,587 返回信息:{'kind': 'Status', 'apiVersion': 'v1', 'metadata': {}, 'status': 'Failure', 'message': 'the body of the request was in an unknown format - accepted media types include: application/json, application/yaml, application/vnd.kubernetes.protobuf', 'reason': 'UnsupportedMediaType', 'code': 415}

Process finished with exit code 0

```

通过控制平台查询结果如下：

```

[root@master ~]# kubectl get deployments -n default

NAME               READY   UP-TO-DATE   AVAILABLE   AGE

kube-mariadb       1/1     1            1           35d

kube-redis         1/1     1            1           35d

kube-zookeeper     1/1     1            1           35d

nginx-deployment   3/3     3            3           122m

```

#### 3. Service 资源管理
（1）接口说明

Service API说明网址：

[https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#create-service-v1-core](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#create-service-v1-core)

创建Service：

HTTP 请求：

POST /api/v1/namespaces/{namespace}/services

| 路径参数 | 描述 |
| --- | --- |
| namespace | 命名空间名称 |

Body参数

| **参数** | **描述** |
| --- | --- |
| body | Services yaml |

其他接口类似：

| 创建 | POST /api/v1/namespaces/{namespace}/services |
| --- | --- |
| 删除 | DELETE /api/v1/namespaces/{namespace}/services /{name} |
| 查找 | GET /api/v1/namespaces/{namespace}/services
GET /api/v1/namespaces/{namespace}/services/{name} |
| 更新 | PUT /api/v1/namespaces/{namespace}/services/{name} |

（2）代码实现

创建service.yaml文件，用于创建内容如下：

```

apiVersion: v1

kind: Service

metadata:

  name: nginx-svc

  namespace: default

spec:

  selector:

    app: nginx

  ports:

    - port: 80

      targetPort: 80

      nodePort: 30080

  type: NodePort

```

再创建一个update_service.yaml，用于测试更新service。

```

spec:

  ports:

    - port: 80

      targetPort: 8089

```

创建api_service_manager.py，进行Service的增删查改操作，代码如下：

```

import requests,time

import logging

import os,yaml,json

#-----------logger-----------

#get logger

logger = logging.getLogger(__name__)

# level

logger.setLevel(logging.DEBUG)

# format

format = logging.Formatter('%(asctime)s %(message)s')

# to console

stream_handler  = logging.StreamHandler()

stream_handler .setFormatter(format)

logger.addHandler(stream_handler )

#-----------logger-----------

#服务接口

#https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#create-service-v1-core

def get_api_server_token(api_server_token, node_url):

    # Bearer token

    bearer_token = "bearer " + api_server_token

    return bearer_token

class api_service_manager():

    def __init__(self,node_url: str, bearer_token: str):

        self.node_url = node_url

        self.bearer_token = bearer_token

    #创建svc

    def create_svc(self, yamlFile, namespace: str):

        headers = {

            "Authorization": self.bearer_token,

            "Content-Type": "application/json"

        }

        # 读取yaml文件，并转化为JSON数据

        with open(yamlFile, encoding="utf8") as f:

            body = json.dumps(yaml.safe_load(f))

        request_url = self.node_url + "/api/v1/namespaces/" + namespace + "/services"

        result = json.loads(requests.post(request_url, data=body, headers=headers, verify=False).text)

        logger.debug(f"返回信息:{str(result)}")

        return result

    #获取svc，如果想指定获取某个svc的信息，可以加上svc_name,若不想则去掉。

    def get_svc(self,svc_name:str,namespace:str):

        headers = {

            "Authorization": self.bearer_token,

            "pretty" : "true"

        }

        request_url = self.node_url + "/api/v1/namespaces/" + namespace + "/services/" + svc_name

        result = json.loads(requests.get(request_url, headers=headers, verify=False).text)

        logger.debug(f"返回信息:{str(result)}")

        return result

    #更改svc,将targetPort从80更改为8080

    def update_svc(self,svc_name:str,yamlFile,namespace:str):

        headers = {

            "Authorization": self.bearer_token,

            "Content-Type": "application/strategic-merge-patch+json"

        }

        with open(yamlFile, encoding="utf8") as f:

            body = json.dumps(yaml.safe_load(f))

        #        '{"spec": {"ports": [{"port": 80, "targetPort": 8089}]}}'

        # body = {"spec": {"ports": [{"port": 80, "targetPort": 80}]}}

        request_url = self.node_url + "/api/v1/namespaces/" + namespace + "/services/" + svc_name

        resp = requests.put(request_url, data=json.dumps(body), headers=headers, verify=False)

        result = json.loads(resp.text)

        logger.debug(f"返回信息:{str(result)}")

        return result

    #删除svc

    def delete_svc(self,svc_name:str,namespace:str):

        headers = {

            "Authorization": bearer_token

        }

        request_url = self.node_url + "/api/v1/namespaces/" + namespace + "/services/" + svc_name

        result = json.loads(requests.delete(request_url, headers=headers, verify=False).text)

        logger.debug(f"返回信息:{str(result)}")

        return result

if __name__ == "__main__":

    #使用自建token

    # 将获取到的token值放到api_server_token中去

    api_server_token = "eyJhbGciOiJS…9HvXA"

    # 节点URL地址 192.168.138.201

    cluster_server_url = "https://192.168.138.201:6443"

    bearer_token = get_api_server_token(api_server_token, cluster_server_url)

    svc_m = api_service_manager(cluster_server_url,bearer_token)

    namespace = "default"

    svc_name = "nginx-svc"

    yaml_file = "service.yaml"

    #创建svc

    print("crate ----------------")

    # svc_m.create_svc(yaml_file, namespace)

    #获取svc

    print("get ----------------")

    svc_m.get_svc(svc_name,namespace)

    #更新svc

    print("patch ----------------")

    svc_m.update_svc(svc_name,"service_update.yaml", namespace)

    """

    #删除svc

    svc_m.delete_svc(svc_name,namespace)

"""

```

（3）代码验证

Service资源管理执行结果为：

```

#python.exe api_tools/api_service_manager.py

Python36\lib\site-packages\urllib3\connectionpool.py:847: InsecureRequestWarning: Unverified HTTPS request is being made. Adding certificate verification is strongly advised. See: [https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings](https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings)

  InsecureRequestWarning)

2022-03-14 17:01:26,714 返回信息:{'kind': 'Service', 'apiVersion': 'v1', 'metadata': {'name': 'nginx-svc', 'namespace': 'default', 'selfLink': '/api/v1/namespaces/default/services/nginx-svc', 'uid': '34532dac-9f0a-4630-bef9-f51897956b57', 'resourceVersion': '82448', 'creationTimestamp': '2022-03-14T09:01:26Z', 'managedFields': [{'manager': 'python-requests', 'operation': 'Update', 'apiVersion': 'v1', 'time': '2022-03-14T09:01:26Z', 'fieldsType': 'FieldsV1', 'fieldsV1': {'f:spec': {'f:externalTrafficPolicy': {}, 'f:ports': {'.': {}, 'k:{"port":80,"protocol":"TCP"}': {'.': {}, 'f:nodePort': {}, 'f:port': {}, 'f:protocol': {}, 'f:targetPort': {}}}, 'f:selector': {'.': {}, 'f:app': {}}, 'f:sessionAffinity': {}, 'f:type': {}}}}]}, 'spec': {'ports': [{'protocol': 'TCP', 'port': 80, 'targetPort': 80, 'nodePort': 30080}], 'selector': {'app': 'nginx'}, 'clusterIP': '10.110.196.244', 'type': 'NodePort', 'sessionAffinity': 'None', 'externalTrafficPolicy': 'Cluster'}, 'status': {'loadBalancer': {}}}

Process finished with exit code 0

```

通过控制平台查询结果如下：

```

[root@master ~]# kubectl get services  -n default

NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE

kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        34d

nginx-svc    NodePort    10.110.196.244   <none>        80:30080/TCP   15m

```

## 案例三、通过Kubernet SDK编写Python运维案例
案例目标

（1）了解Kubernetes Python Client。

（2）了解Pod、Deployment、Service、Job等资源管理代码Python编写方法。

### 实施准备
（1）了解Kubernetes Python Client：

参考文档：[https://kubernetes.readthedocs.io/en/latest/index.html](https://kubernetes.readthedocs.io/en/latest/index.html)

源代码：[https://github.com/kubernetes-client/python](https://github.com/kubernetes-client/python)

（2）了解Pod、Service、Deployment等资源类型管理Python SDK编写方法。增删查改。

Kubernetes Python SDK是包是由[OpenAPI Generator](https://openapi-generator.tech/)项目自动生成的。API 版本：release-1.23

通过读取config文件方式登录。文件从Kubernetes的配置目录复制到代码所在目录下：

/root/.kube/config

#### 1. Pod资源管理
（1）接口说明

资源接口对象：kubernetes.client.CoreV1Api

资源接口方法

| CoreV1Api.create_namespaced_pod | 创建Pod |
| --- | --- |
| CoreV1Api.read_namespaced_pod | 读取Pod |
| CoreV1Api.delete_namespaced_pod | 删除Pod |
| CoreV1Api.patch_namespaced_pod | 更新Pod |

（2）代码实现

```bash

import os

import yaml

from kubernetes import client, config

class pod_manager():

    def __init__(self,config_file):

        #传入配置文件

        config.load_kube_config(config_file)

        #获取API,管理Pod

        self.api = client.CoreV1Api()

    #创建Pod

    def create_pod(self,yamlFile):

        # 获取当前文件的绝对路径

        fileNamePath = os.path.split(os.path.realpath(__file__))[0]

        # 获取yaml配置文件的路径

        yamlPath = os.path.join(fileNamePath,yamlFile)

        #读取yaml文件，并转化为JSON数据

        with open(yamlPath,encoding="utf8") as f:

            result = yaml.safe_load(f) #转化成JSON格式

            resp = self.api.create_namespaced_pod(

                namespace="default",

                body=result,

            )

            #print(resp)

            #打印出创建时的具体信息

            print("\n[INFO] Pod `nginx-deployment` created.\n")

    #查看Pod

    def get_pod(self):

        v1 = self.api

        resp = v1.read_namespaced_pod(name="nginx-deployment",namespace="default")

        #print(resp)

        print("\n[INFO] Pod `nginx-deployment` is read.\n")

    #修改Pod的镜像（改）

    def update_pod(self):

        v1 = self.api

        old_resp = v1.read_namespaced_pod(name="nginx-deployment",namespace="default")

        #修改镜像

        old_resp.spec.containers[0].image = "nginx:1.16.0"

        new_resp = v1.patch_namespaced_pod(name="nginx-deployment",namespace="default",body=old_resp)

        #print(new_resp)

        #打印信息

        print("\n[INFO] Update the image to nginx: 1.16.0 \n")

    #删除Pod（删）

    def delete_pod(self):

        v1 = self.api

        resp = v1.delete_namespaced_pod(name="nginx-deployment", namespace="default")

        #print(resp)

        print("\n[INFO] The Pod `nginx-deployment` is Deleted \n")

if __name__ == '__main__':

    pod_manager(config_file="config").create_pod(yamlFile="nginx-pod.yaml")

    pod_manager(config_file="config").get_pod()

    pod_manager(config_file="config").update_pod()

    # pod_manager(config_file="config").delete_pod()

```

登录Kubernetes平台进行查看。

（3）代码验证

执行代码，结果如下（部分）：

```

#python.exe  sdk_pod_manager.py

{'api_version': 'v1',

 'kind': 'Pod',

 'metadata': {'annotations': None,

              'cluster_name': None,

              'creation_timestamp': datetime.datetime(2022, 3, 15, 6, 32, 55, tzinfo=tzutc()),

                                              'terminated': None,

                                              'waiting': {'message': None,

                                                          'reason': 'ContainerCreating'}}}],

            'ephemeral_container_statuses': None,

            'host_ip': '192.168.138.201',

            'init_container_statuses': None,

            'message': None,

            'nominated_node_name': None,

            'phase': 'Pending',

            'pod_i_ps': None,

            'pod_ip': None,

            'qos_class': 'BestEffort',

            'reason': None,

            'start_time': datetime.datetime(2022, 3, 15, 6, 32, 55, tzinfo=tzutc())}}

[INFO] Update the image to nginx: 1.16.0

Process finished with exit code 0

```

可以通过命令行查询信息：

[root@master ~]# kubectl get pods

NAME                                READY   STATUS    RESTARTS   AGE

kube-mariadb-6dd9d5946-6j4zw        1/1     Running   1          35d

kube-redis-7f6484cd59-dp6ln         1/1     Running   1          35d

kube-zookeeper-577b9d598f-n946k     1/1     Running   1          35d

nginx-deployment                    1/1     Running   0          30s

使用SDK非常容易，Deployment、Service、Job等资源管理的调用可以查看文档与源码案例。Github开源的案例：

[https://github.com/kubernetes-client/python/tree/master/examples](https://github.com/kubernetes-client/python/tree/master/examples)

## 案例三、命令行交互工具开发
案例目标

（1）了解交互式命令行框架click。

（2）使用click开发案例。

### 实施准备
云平台操作都提供console客户端工具，如openstack、kubectl，以下展示如何通过Python Click框架开发命令行工具，click库是一个利用很少的代码以可组合的方式创造优雅命令行工具接口的 Python 库。

Click的帮助文件参看：[https://click-docs-zh-cn.readthedocs.io/zh/latest/](https://click-docs-zh-cn.readthedocs.io/zh/latest/)。

Click 的三个特性:

（1）任意嵌套命令：如封装“kubectl get pods -o wide”类似命令。

（2）自动生成帮助页面：如封装“kubectl –help”查看帮助文件。

（3）支持在运行时延迟加载子命令。如运行后台Web服务等。

Click安装非常简单：pip install click。

### 案例实施
#### 1. Click案例
模拟一个多云操作命令onecloud，其中云厂商用1代表openstack、2代表kubectl、3代表华为云。创建oneclick.py，代码如下：

```

import click

import os

"""

Click 的使用大致有两个步骤：

使用 @click.command() 装饰一个函数，使之成为命令行接口；

使用 @click.option() 等装饰函数，为其添加命令行选项等。

[https://click-docs-zh-cn.readthedocs.io/zh/latest/](https://click-docs-zh-cn.readthedocs.io/zh/latest/)

这里封装一个onecloud 命令行工具，通过onecloud 操作openstack、k8s、华为云等。

每个平台都有主机ip、账号、密码或ak/sk。

为减少参数输入，这里通过读取配置文件oncloud.yaml来完成。

"""

# 命令

@click.command()

#参数c（cloud platform id） 数量 ，命令行参赛，默认值 ，提示，帮助

@click.option('--c', default=1, prompt='--c Cloud Platform [1] OpenStack,[2]Kubernetes[3]Huawei Cloud',help='Cloud Platform ID.')

#参数2 名称 提示

@click.option('--a', prompt='Action Name', help='Impurt action Name, such As list server.')

def onecloud(c, a):

    """模拟一个多云操作命令行工具"""

    #openstack

    if c == 1:

        click.echo('openstack %s!' % a)

        cmd = "openstack " + a

        os.system(cmd)

    """模拟一个多云操作命令行工具"""

    if c == 2:

        click.echo('kubectl %s!' % a)

    """模拟一个多云操作命令行工具"""

    if c == 3:

        click.echo('huawei %s!' % a)

if __name__ == '__main__':

    onecloud()

```

（1）通过--c cloud ，选型选择平台。

（2）通过-a action，选择命令。

测试情况如下：

**（1）查看帮助，执行如下：**

```

PS D:\1daoyun\2022\cloudpython> python onecloud.py --help

Usage: onecloud.py [OPTIONS]

```

  模拟一个多云操作命令行工具

```

Options:

  --c INTEGER  Cloud Platform ID.

  --a TEXT     Impurt action Name, such As list server.

  --help       Show this message and exit.

```

**（2）通过提升交互进行操作，如下：**

```

PS D:\1daoyun\2022\cloudpython> python onecloud.py

--c Cloud Platform [1] OpenStack,[2]Kubernetes[3]Huawei Cloud [1]: 1

Action Name: list images

openstack list images!

```

（3）通过输入参数进行操作，如下：

```
PS D:\1daoyun\2022\cloudpython> python onecloud.py --c 1 --a "list images"

openstack list images!
```

如果程序直接调用APIs已经开发的模块，即可以实现相关具体操作。
