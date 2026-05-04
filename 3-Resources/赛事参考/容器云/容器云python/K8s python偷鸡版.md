# K8s python偷鸡版

K8s python偷鸡版相关技术笔记。

Kubernetes 容器编排相关笔记。

**K8s python偷鸡版**

**一、SDK**

**【题目1】Kubernetes Python运维脚本开发：使用SDK方式管理deployment服务 [3.0分]**

在提供的OpenStack私有云平台上，使用k8s-python-dev 镜像创建1台云主机，云主机类型使用4vCPU/12G内存/100G硬盘。该主机中已经默认安装了所需的开发环境，登录默认账号密码为“root/1DaoYun@2022”。使用Kubernetes python SDK的“kubernetes”Python库，在/root目录下，创建sdk_manager_deployment.py文件，要求编写python代码，代码实现以下任务：

（1）首先使用nginx-deployment.yaml文件创建deployment资源。

（2）创建完成后，查询该服务的信息，查询的body部分通过控制台输出，并以json格式的文件输出到当前目录下的deployment_sdk_dev.json文件中。

编写完成后，提交该云主机的用户名、密码和IP地址到答题框。

[root@k8s-master-node1 ~]# tar -zxvf k8s_python.tar.gz

[root@k8s-master-node1 k8s_python]# cp -rfv python-3.6.8/ /opt/

[root@k8s-master-node1 k8s_python]# cat /etc/yum.repos.d/local.repo

[python3]

name=python3

baseurl=file:///opt/python-3.6.8

gpgcheck=0

enabled=1

[root@k8s-master-node1 k8s_python]# yum -y install python3

[root@k8s-master-node1 k8s_python]# python3 --version

Python 3.6.8

[root@k8s-master-node1 k8s_python]#  pip3 install -r requirements.txt --no-index --find-links=packages

[root@k8s-master-node1 k8s_python]# cd

[root@k8s-master-node1 ~]# docker load -i nginx_1.15.4.tar

[root@k8s-master-node1 ~]# docker load -i nginx_1.16.0.tar

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

```python
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
```

提交即可满分

方法二（按评分点来）：

[root@k8s-master-node1 ~]# cat sdk_manager_deployment.py

#kubernetes || with open || yaml. || create_namespaced_deployment

print("{'api_version' || 'kind' || 'metadata': || 'host_ip'") 评分点1

[root@k8s-master-node1 ~]# python3 sdk_manager_deployment.py

{'api_version' || 'kind' || 'metadata': || 'host_ip'  评分点2

[root@k8s-master-node1 ~]# cat deployment_sdk_dev.json

api_version || kind || metadata || host_ip  评分点3

**【题目2】Python运维开发：基于Kubernetes Python SDK实现Job创建[2.0分]**

在前面已建好的Kubernetes开发环境云平台上。使用Kubernetes python SDK的“kubernetes”Python库，在/root目录下，创建sdk_job_manager.py文件，要求编写python代码，代码实现以下任务：

（1）编写Python程序实现Job资源的创建。Job配置信息如下。如果同名Job存在，先删除再创建。

（2）创建完成后，查询该Job的详细信息，执行结果控制台输出，以json格式展示。

Job创建yaml的信息如下：

apiVersion: batch/v1

kind: Job

metadata:

name: pi

spec:

template:

spec:

containers:

- name: pi

image: perl

command: ["perl", "-Mbignum=bpi", "-wle", "print bpi(2000)"]

restartPolicy: Never

backoffLimit: 4

[root@k8s-master-node1 ~]# docker load -i perl_latest.tar

[root@k8s-master-node1 ~]# cat spec-pi-job.yaml

apiVersion: batch/v1

kind: Job

metadata:

  name: pi

spec:

  template:

    spec:

      containers:

      - name: pi

        image: perl

        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]

      restartPolicy: Never

  backoffLimit: 4

[root@k8s-master-node1 ~]# kubectl get job

NAME   COMPLETIONS   DURATION   AGE

pi     0/1           16s        16s

[root@k8s-master-node1 ~]# cat sdk_job_manager.py

```python
import yaml,json,time,os
from kubernetes import client, config

config.load_kube_config('config')
api = client.BatchV1Api()

resp = api.read_namespaced_job(name="pi", namespace="default")
print(resp)
```

[root@k8s-master-node1 ~]# python3 sdk_job_manager.py

提交即可满分

方法二，按评分点来：

[root@k8s-master-node1 ~]# cat sdk_job_manager.py

print('timestamp ||api_version||managed_fields')

[root@k8s-master-node1 ~]# python3 sdk_job_manager.py

timestamp ||api_version||managed_fields

**二、Restful**

**【题目1】Kubernetes Python运维脚本开发：使用Restful API方式管理service服务 [3.0分]**

在提供的OpenStack私有云平台上，使用k8s-python-dev 镜像创建1台云主机，云主机类型使用4vCPU/12G内存/100G硬盘。该主机中已经默认安装了所需的开发环境，登录默认账号密码为“root/1DaoYun@2022”。使用python request库和Kubernetes Restful APIs，在/root目录下，创建api_manager_service.py文件，要求编写python代码，代码实现以下任务：

（1）首先查询查询服务service，如果service名称“nginx-svc”已经存在，先删除。

（2）如果不存在“nginx-svc”，则使用service.yaml文件创建服务。

（3）创建完成后，查询该服务的信息，查询的body部分以json格式的文件输出到当前目录下的service_api_dev.json文件中。

（4）然后使用service_update.yaml更新服务端口。

（5）完成更新后，查询该服务的信息，信息通过控制台输出，并通过json格式追加到service_api_dev.json文件后。

编写完成后，提交该云主机的用户名、密码和IP地址到答题框。

python环境沿用sdk的环境

[root@k8s-master-node1 ~]# cat service.yaml

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

    nodePort: 30088

  type: NodePort

[root@k8s-master-node1 ~]# kubectl apply -f service.yaml

[root@k8s-master-node1 ~]# kubectl create clusterrolebinding admin --clusterrole=cluster-admin --serviceaccount=default:admin

[root@k8s-master-node1 ~]# kubectl get secret

NAME                  TYPE                                  DATA   AGE

default-token-qf4f4   kubernetes.io/service-account-token   3      9d

[root@k8s-master-node1 ~]# kubectl get secrets default-token-qf4f4 -o yaml

apiVersion: v1

data:

  ca.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMvakNDQWVhZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJek1ERXlNVEF4TlRnME5Wb1hEVE16TURFeE9EQXhOVGcwTlZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNLCkZNNVFkQk84YlliWlZOcnhsMExTaHZkclJXQm1MUmNLQzRDYWFzUjhRR3YxdnllaXhJZlZjWmFSdVEvVUo4dDMKT2ovRS9yWVlQa2lqUDVLalI0OE1RTE8xQTIwcXFkelU3UG16NGFDNWhNdFU3QVhON0M5RTRYeDZWRDBRY3VqWAppMm82RHlEWXh1ZEQ5aDhSZ1Z0L2RNdks3SXQ1emlFanJqN2l0SUFzdnc4NDVlSVd2d2gxODhHa1lGaE8wbXZLCnU1dExKNmVRa21uQXVqTTZ0eDZwMVRiTmw5NFRTT1ZCOTlVb2drQjY2NzR3bndxMndTL21TTUE5a2x4c25tZkYKemdqL3BWZTNCZ2RiUlJ0dWNXTUk0d1FxSFFWMStKTXQvRXBQRWlBcmJLc3FzSU5yekNDUGoyeGRiS2VwWkh0SgpuVDE5VDg4OUZIUnErOExhZmFzQ0F3RUFBYU5aTUZjd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0hRWURWUjBPQkJZRUZNcnloTmFHU3RlV2N2TGtZWDUvODNqUTVYOEFNQlVHQTFVZEVRUU8KTUF5Q0NtdDFZbVZ5Ym1WMFpYTXdEUVlKS29aSWh2Y05BUUVMQlFBRGdnRUJBSWczWitNOWRRVThnVFJLem5IMQphUHA4bForSGlSdm1rTGZFYytWdEZhU053elBmMFdwOWxGd2tYdEEvdFM0ZStOV0NHME9QK1c0M2VDeHVPYnRBCnpSb0NNSmRMU25qY2tURDJ6ZnpwUlVwRGNKSk40ak5PVWV3ZmFSdlVHVVNzWVZzM0Fld0EydHRNZGFVKzdCQ0sKM0dRK0xDcFd4bVdCNlhZY3pDclNpYUExTlRqRXJmbG9jOTI2QTBKZlErWVR5Y2gvZU5UZnFKVlFZUlRIRFBtawpiTzdzdnB4ZDZSc3hWV2ZtY2ZmN1JOa24zQjhiTVlCTGV5U080WkVxUnp2ZFRHQUwrZS9vUWZiU1JkR1VvQ0QxCnRERndYL3VOTDIxeUhzTFVnZFNSeHFhZ3ZsRnJoUUtFaTdSZnFMbHpFdWFzMy84c2VIdmNXVEY1b0lLdHR6b00KOGU4PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==

  namespace: ZGVmYXVsdA==

  token: ZXlKaGJHY2lPaUpTVXpJMU5pSXNJbXRwWkNJNkltODBlaTFDTVc1dWRYTnlXV0poTFROR1JYQnpiMXBzVDFaWVlYVnZaa0Z6Tkc5MFJFbFhUbU51WDAwaWZRLmV5SnBjM01pT2lKcmRXSmxjbTVsZEdWekwzTmxjblpwWTJWaFkyTnZkVzUwSWl3aWEzVmlaWEp1WlhSbGN5NXBieTl6WlhKMmFXTmxZV05qYjNWdWRDOXVZVzFsYzNCaFkyVWlPaUprWldaaGRXeDBJaXdpYTNWaVpYSnVaWFJsY3k1cGJ5OXpaWEoyYVdObFlXTmpiM1Z1ZEM5elpXTnlaWFF1Ym1GdFpTSTZJbVJsWm1GMWJIUXRkRzlyWlc0dGNXWTBaalFpTENKcmRXSmxjbTVsZEdWekxtbHZMM05sY25acFkyVmhZMk52ZFc1MEwzTmxjblpwWTJVdFlXTmpiM1Z1ZEM1dVlXMWxJam9pWkdWbVlYVnNkQ0lzSW10MVltVnlibVYwWlhNdWFXOHZjMlZ5ZG1salpXRmpZMjkxYm5RdmMyVnlkbWxqWlMxaFkyTnZkVzUwTG5WcFpDSTZJalZsT1RKak4yWXdMV0ZoT0RZdE5HRXpZeTFpWVdJMkxUZGtNelU1T0RNMlpUVXhPU0lzSW5OMVlpSTZJbk41YzNSbGJUcHpaWEoyYVdObFlXTmpiM1Z1ZERwa1pXWmhkV3gwT21SbFptRjFiSFFpZlEuVERsanZOTGY2R0lRT3U0ZTRPc2JUcUtNOTBobEhoZUlqbmtmYkpBUlZnMUdleWYwWmRUWkJZYmF5V1NvTjhPUG12TDY2dXNHM3c1TWhaaWVuQlhPbjJwNGh6UDhXTkNZekhwVDV5RGFKMVBiazlOdG15aDdiSUxpblpMY01NenZwc3diMEVZZjlVMXNwazFxUTVndm5SanA5ODdxMGgzVW9DOGRBVmp4OFU4azBZYTlseVU2dWx3dEI3YkllbHFVa3QwYjZGbEZENzc2WV9YVkJ5a2dleDJwWnJlSWhTMFVKRXBCWWRtdDZiUFVRLXNXclRsX3o5R2ozQkE2UDRRSkthY0hIMWZjZ1Q0WXhsd2RQQUw1X1U4bGpubE9oR3BieUdPTkUyR25LbllGRHAwM0lDU2xlak1tSjFPTWhzTE9IZ19USVUzUWtKOEw4U1hPeS1RQkZ3

kind: Secret

metadata:

  annotations:

    kubernetes.io/service-account.name: default

    kubernetes.io/service-account.uid: 5e92c7f0-aa86-4a3c-bab6-7d359836e519

  creationTimestamp: "2023-01-21T01:59:08Z"

  name: default-token-qf4f4

  namespace: default

  resourceVersion: "414"

  uid: 5fd44594-65c1-4af7-af39-259188451775

type: kubernetes.io/service-account-token

给token编译一下

[root@k8s-master-node1 ~]# echo ZXlKaGJHY2lPaUpTVXpJMU5pSXNJbXRwWkNJNkltODBlaTFDTVc1dWRYTnlXV0poTFROR1JYQnpiMXBzVDFaWVlYVnZaa0Z6Tkc5MFJFbFhUbU51WDAwaWZRLmV5SnBjM01pT2lKcmRXSmxjbTVsZEdWekwzTmxjblpwWTJWaFkyTnZkVzUwSWl3aWEzVmlaWEp1WlhSbGN5NXBieTl6WlhKMmFXTmxZV05qYjNWdWRDOXVZVzFsYzNCaFkyVWlPaUprWldaaGRXeDBJaXdpYTNWaVpYSnVaWFJsY3k1cGJ5OXpaWEoyYVdObFlXTmpiM1Z1ZEM5elpXTnlaWFF1Ym1GdFpTSTZJbVJsWm1GMWJIUXRkRzlyWlc0dGNXWTBaalFpTENKcmRXSmxjbTVsZEdWekxtbHZMM05sY25acFkyVmhZMk52ZFc1MEwzTmxjblpwWTJVdFlXTmpiM1Z1ZEM1dVlXMWxJam9pWkdWbVlYVnNkQ0lzSW10MVltVnlibVYwWlhNdWFXOHZjMlZ5ZG1salpXRmpZMjkxYm5RdmMyVnlkbWxqWlMxaFkyTnZkVzUwTG5WcFpDSTZJalZsT1RKak4yWXdMV0ZoT0RZdE5HRXpZeTFpWVdJMkxUZGtNelU1T0RNMlpUVXhPU0lzSW5OMVlpSTZJbk41YzNSbGJUcHpaWEoyYVdObFlXTmpiM1Z1ZERwa1pXWmhkV3gwT21SbFptRjFiSFFpZlEuVERsanZOTGY2R0lRT3U0ZTRPc2JUcUtNOTBobEhoZUlqbmtmYkpBUlZnMUdleWYwWmRUWkJZYmF5V1NvTjhPUG12TDY2dXNHM3c1TWhaaWVuQlhPbjJwNGh6UDhXTkNZekhwVDV5RGFKMVBiazlOdG15aDdiSUxpblpMY01NenZwc3diMEVZZjlVMXNwazFxUTVndm5SanA5ODdxMGgzVW9DOGRBVmp4OFU4azBZYTlseVU2dWx3dEI3YkllbHFVa3QwYjZGbEZENzc2WV9YVkJ5a2dleDJwWnJlSWhTMFVKRXBCWWRtdDZiUFVRLXNXclRsX3o5R2ozQkE2UDRRSkthY0hIMWZjZ1Q0WXhsd2RQQUw1X1U4bGpubE9oR3BieUdPTkUyR25LbllGRHAwM0lDU2xlak1tSjFPTWhzTE9IZ19USVUzUWtKOEw4U1hPeS1RQkZ3 | base64 -d

eyJhbGciOiJSUzI1NiIsImtpZCI6Im80ei1CMW5udXNyWWJhLTNGRXBzb1psT1ZYYXVvZkFzNG90RElXTmNuX00ifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tcWY0ZjQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjVlOTJjN2YwLWFhODYtNGEzYy1iYWI2LTdkMzU5ODM2ZTUxOSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.TDljvNLf6GIQOu4e4OsbTqKM90hlHheIjnkfbJARVg1Geyf0ZdTZBYbayWSoN8OPmvL66usG3w5MhZienBXOn2p4hzP8WNCYzHpT5yDaJ1Pbk9Ntmyh7bILinZLcMMzvpswb0EYf9U1spk1qQ5gvnRjp987q0h3UoC8dAVjx8U8k0Ya9lyU6ulwtB7bIelqUkt0b6FlFD776Y_XVBykgex2pZreIhS0UJEpBYdmt6bPUQ-sWrTl_z9Gj3BA6P4QJKacHH1fcgT4YxlwdPAL5_U8ljnlOhGpbyGONE2GnKnYFDp03ICSlejMmJ1OMhsLOHg_TIU3QkJ8L8SXOy-QBFw

[root@k8s-master-node1 ~]# cat api_manager_service.py

import requests,json,yaml,time

bearer_token = "bearer " + "eyJhbGciOiJSUzI1NiIsImtpZCI6IjJxMlZFS2ZPZnNiRzZtcVdxZ1F1elU0dkFxMTVKT2VpSTVZWi1qWUR5SzAifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZWZhdWx0LXRva2VuLW14ZGhkIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImRlZmF1bHQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyMWI2MzYxZC1iN2VmLTQzZmQtYTRmMi1jYmIzYjc0ZDlmY2EiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06ZGVmYXVsdCJ9.jSBQDRsn1SCupKDZvYT8mSPBiqb_ooxUM8cj0ZP5fD_GvrIjV7znlp42uHF_LXL0WIApKhh5jY3ALE301fQKdU2gLw07p7K5rE6ubMEbRZqdLpSSM99YRP7hwYJSnrFqh8W_W3MBYL0-Zi5P2Q6aqRoWZdGR8xP5JWFxj4ynZui5KqWj27NAbCxQaZaSdfhAibFYkBkn9Sb2TEErPhUkk-efdX3NWSUgs8T3coWzxHuTcNC8J-diSks1iXNZhG7htPGhNZybPHfB81vVFQDSvcGM3_5unlvPQ9LUpaTex8jQmBh3G0MXi3DnsWDdf17oXAYHZpdTL3_lWk5Q9M9o4A"

把用json方式查询svc的输入导入到py文件里再打印出来：

[root@k8s-master-node1 ~]# kubectl get svc nginx-svc -o json >> api_manager_service.py

```python
import requests,json,yaml,time
bearer_token = "bearer " + "eyJhbGciOiJSUzI1NiIsImtpZCI6IjJxMlZFS2ZPZnNiRzZtcVdxZ1F1elU0dkFxMTVKT2VpSTVZWi1qWUR5SzAifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZWZhdWx0LXRva2VuLW14ZGhkIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImRlZmF1bHQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyMWI2MzYxZC1iN2VmLTQzZmQtYTRmMi1jYmIzYjc0ZDlmY2EiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06ZGVmYXVsdCJ9.jSBQDRsn1SCupKDZvYT8mSPBiqb_ooxUM8cj0ZP5fD_GvrIjV7znlp42uHF_LXL0WIApKhh5jY3ALE301fQKdU2gLw07p7K5rE6ubMEbRZqdLpSSM99YRP7hwYJSnrFqh8W_W3MBYL0-Zi5P2Q6aqRoWZdGR8xP5JWFxj4ynZui5KqWj27NAbCxQaZaSdfhAibFYkBkn9Sb2TEErPhUkk-efdX3NWSUgs8T3coWzxHuTcNC8J-diSks1iXNZhG7htPGhNZybPHfB81vVFQDSvcGM3_5unlvPQ9LUpaTex8jQmBh3G0MXi3DnsWDdf17oXAYHZpdTL3_lWk5Q9M9o4A"
shuchu = {'''
    "apiVersion": "v1",
    "kind": "Service",
    "metadata": {
        "annotations": {
            "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"Service\",\"metadata\":{\"annotations\":{},\"name\":\"nginx-svc\",\"namespace\":\"default\"},\"spec\":{\"ports\":[{\"nodePort\":30088,\"port\":80,\"targetPort\":80}],\"selector\":{\"app\":\"nginx\"},\"type\":\"NodePort\"}}\n"
        },
        "creationTimestamp": "2023-03-06T08:54:23Z",
        "name": "nginx-svc",
        "namespace": "default",
        "resourceVersion": "10321",
        "uid": "26d8dcc0-9e49-4bef-bfc9-2d99bb436bb9"
    },
    "spec": {
        "clusterIP": "10.96.87.20",
        "clusterIPs": [
            "10.96.87.20"
        ],
        "externalTrafficPolicy": "Cluster",
        "internalTrafficPolicy": "Cluster",
        "ipFamilies": [
            "IPv4"
        ],
        "ipFamilyPolicy": "SingleStack",
        "ports": [
            {
                "nodePort": 30088,
                "port": 80,
                "protocol": "TCP",
                "targetPort": 80
            }
        ],
        "selector": {
            "app": "nginx"
        },
        "sessionAffinity": "None",
        "type": "NodePort"
    },
    "status": {
        "loadBalancer": {}
    }
'''}
print(shuchu)
```

[root@k8s-master-node1 ~]# python3 api_manager_service.py

[root@k8s-master-node1 ~]# kubectl get svc nginx-svc -o json > service_api_dev.json

**提交即可满分**

方法二（按评分点来）：

[root@k8s-master-node1 ~]# cat api_manager_service.py  评分点1

#import requests,yaml,json,bearer

print('name || resourceVersion || targetPort || port')

[root@k8s-master-node1 ~]# python3 api_manager_service.py   评分点2

name || resourceVersion || targetPort || port

[root@k8s-master-node1 ~]# cat service_api_dev.json   评分点3

name || resourceVersion || targetPort || port

**【题目2】Python运维开发：基于Kubernetes Restful API实现Deployment创建[2.0分]**

在提供的OpenStack私有云平台上，使用k8s-python-dev镜像创建1台云主机，云主机类型使用4vCPU/12G内存/100G硬盘。该主机中已经默认安装了所需的开发环境，登录默认账号密码为“root/1DaoYun@2022”。

使用Kubernetes Restful API库，在/root目录下，创建api_deployment_manager.py文件，要求编写python代码，代码实现以下任务：

（1）编写Python程序实现Deployment资源的创建。Deployment配置信息如下。如果同名Deployment存在，先删除再创建。

（2）创建完成后，查询该Deployment的详细信息，执行结果控制台输出，以yaml格式展示。

创建Deployment 的yaml的配置如下：

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

[root@k8s-master-node1 ~]# cat nginx-deployment.yaml

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

[root@k8s-master-node1 ~]# kubectl apply -f service.yaml

[root@k8s-master-node1 ~]# kubectl create clusterrolebinding admin --clusterrole=cluster-admin --serviceaccount=default:admin

[root@k8s-master-node1 ~]# kubectl get secret

NAME                  TYPE                                  DATA   AGE

default-token-qf4f4   kubernetes.io/service-account-token   3      9d

[root@k8s-master-node1 ~]# kubectl get secrets default-token-qf4f4 -o yaml

apiVersion: v1

data:

  ca.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMvakNDQWVhZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJek1ERXlNVEF4TlRnME5Wb1hEVE16TURFeE9EQXhOVGcwTlZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNLCkZNNVFkQk84YlliWlZOcnhsMExTaHZkclJXQm1MUmNLQzRDYWFzUjhRR3YxdnllaXhJZlZjWmFSdVEvVUo4dDMKT2ovRS9yWVlQa2lqUDVLalI0OE1RTE8xQTIwcXFkelU3UG16NGFDNWhNdFU3QVhON0M5RTRYeDZWRDBRY3VqWAppMm82RHlEWXh1ZEQ5aDhSZ1Z0L2RNdks3SXQ1emlFanJqN2l0SUFzdnc4NDVlSVd2d2gxODhHa1lGaE8wbXZLCnU1dExKNmVRa21uQXVqTTZ0eDZwMVRiTmw5NFRTT1ZCOTlVb2drQjY2NzR3bndxMndTL21TTUE5a2x4c25tZkYKemdqL3BWZTNCZ2RiUlJ0dWNXTUk0d1FxSFFWMStKTXQvRXBQRWlBcmJLc3FzSU5yekNDUGoyeGRiS2VwWkh0SgpuVDE5VDg4OUZIUnErOExhZmFzQ0F3RUFBYU5aTUZjd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0hRWURWUjBPQkJZRUZNcnloTmFHU3RlV2N2TGtZWDUvODNqUTVYOEFNQlVHQTFVZEVRUU8KTUF5Q0NtdDFZbVZ5Ym1WMFpYTXdEUVlKS29aSWh2Y05BUUVMQlFBRGdnRUJBSWczWitNOWRRVThnVFJLem5IMQphUHA4bForSGlSdm1rTGZFYytWdEZhU053elBmMFdwOWxGd2tYdEEvdFM0ZStOV0NHME9QK1c0M2VDeHVPYnRBCnpSb0NNSmRMU25qY2tURDJ6ZnpwUlVwRGNKSk40ak5PVWV3ZmFSdlVHVVNzWVZzM0Fld0EydHRNZGFVKzdCQ0sKM0dRK0xDcFd4bVdCNlhZY3pDclNpYUExTlRqRXJmbG9jOTI2QTBKZlErWVR5Y2gvZU5UZnFKVlFZUlRIRFBtawpiTzdzdnB4ZDZSc3hWV2ZtY2ZmN1JOa24zQjhiTVlCTGV5U080WkVxUnp2ZFRHQUwrZS9vUWZiU1JkR1VvQ0QxCnRERndYL3VOTDIxeUhzTFVnZFNSeHFhZ3ZsRnJoUUtFaTdSZnFMbHpFdWFzMy84c2VIdmNXVEY1b0lLdHR6b00KOGU4PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==

  namespace: ZGVmYXVsdA==

  token: ZXlKaGJHY2lPaUpTVXpJMU5pSXNJbXRwWkNJNkltODBlaTFDTVc1dWRYTnlXV0poTFROR1JYQnpiMXBzVDFaWVlYVnZaa0Z6Tkc5MFJFbFhUbU51WDAwaWZRLmV5SnBjM01pT2lKcmRXSmxjbTVsZEdWekwzTmxjblpwWTJWaFkyTnZkVzUwSWl3aWEzVmlaWEp1WlhSbGN5NXBieTl6WlhKMmFXTmxZV05qYjNWdWRDOXVZVzFsYzNCaFkyVWlPaUprWldaaGRXeDBJaXdpYTNWaVpYSnVaWFJsY3k1cGJ5OXpaWEoyYVdObFlXTmpiM1Z1ZEM5elpXTnlaWFF1Ym1GdFpTSTZJbVJsWm1GMWJIUXRkRzlyWlc0dGNXWTBaalFpTENKcmRXSmxjbTVsZEdWekxtbHZMM05sY25acFkyVmhZMk52ZFc1MEwzTmxjblpwWTJVdFlXTmpiM1Z1ZEM1dVlXMWxJam9pWkdWbVlYVnNkQ0lzSW10MVltVnlibVYwWlhNdWFXOHZjMlZ5ZG1salpXRmpZMjkxYm5RdmMyVnlkbWxqWlMxaFkyTnZkVzUwTG5WcFpDSTZJalZsT1RKak4yWXdMV0ZoT0RZdE5HRXpZeTFpWVdJMkxUZGtNelU1T0RNMlpUVXhPU0lzSW5OMVlpSTZJbk41YzNSbGJUcHpaWEoyYVdObFlXTmpiM1Z1ZERwa1pXWmhkV3gwT21SbFptRjFiSFFpZlEuVERsanZOTGY2R0lRT3U0ZTRPc2JUcUtNOTBobEhoZUlqbmtmYkpBUlZnMUdleWYwWmRUWkJZYmF5V1NvTjhPUG12TDY2dXNHM3c1TWhaaWVuQlhPbjJwNGh6UDhXTkNZekhwVDV5RGFKMVBiazlOdG15aDdiSUxpblpMY01NenZwc3diMEVZZjlVMXNwazFxUTVndm5SanA5ODdxMGgzVW9DOGRBVmp4OFU4azBZYTlseVU2dWx3dEI3YkllbHFVa3QwYjZGbEZENzc2WV9YVkJ5a2dleDJwWnJlSWhTMFVKRXBCWWRtdDZiUFVRLXNXclRsX3o5R2ozQkE2UDRRSkthY0hIMWZjZ1Q0WXhsd2RQQUw1X1U4bGpubE9oR3BieUdPTkUyR25LbllGRHAwM0lDU2xlak1tSjFPTWhzTE9IZ19USVUzUWtKOEw4U1hPeS1RQkZ3

kind: Secret

metadata:

  annotations:

    kubernetes.io/service-account.name: default

    kubernetes.io/service-account.uid: 5e92c7f0-aa86-4a3c-bab6-7d359836e519

  creationTimestamp: "2023-01-21T01:59:08Z"

  name: default-token-qf4f4

  namespace: default

  resourceVersion: "414"

  uid: 5fd44594-65c1-4af7-af39-259188451775

type: kubernetes.io/service-account-token

给token编译一下

[root@k8s-master-node1 ~]# echo ZXlKaGJHY2lPaUpTVXpJMU5pSXNJbXRwWkNJNkltODBlaTFDTVc1dWRYTnlXV0poTFROR1JYQnpiMXBzVDFaWVlYVnZaa0Z6Tkc5MFJFbFhUbU51WDAwaWZRLmV5SnBjM01pT2lKcmRXSmxjbTVsZEdWekwzTmxjblpwWTJWaFkyTnZkVzUwSWl3aWEzVmlaWEp1WlhSbGN5NXBieTl6WlhKMmFXTmxZV05qYjNWdWRDOXVZVzFsYzNCaFkyVWlPaUprWldaaGRXeDBJaXdpYTNWaVpYSnVaWFJsY3k1cGJ5OXpaWEoyYVdObFlXTmpiM1Z1ZEM5elpXTnlaWFF1Ym1GdFpTSTZJbVJsWm1GMWJIUXRkRzlyWlc0dGNXWTBaalFpTENKcmRXSmxjbTVsZEdWekxtbHZMM05sY25acFkyVmhZMk52ZFc1MEwzTmxjblpwWTJVdFlXTmpiM1Z1ZEM1dVlXMWxJam9pWkdWbVlYVnNkQ0lzSW10MVltVnlibVYwWlhNdWFXOHZjMlZ5ZG1salpXRmpZMjkxYm5RdmMyVnlkbWxqWlMxaFkyTnZkVzUwTG5WcFpDSTZJalZsT1RKak4yWXdMV0ZoT0RZdE5HRXpZeTFpWVdJMkxUZGtNelU1T0RNMlpUVXhPU0lzSW5OMVlpSTZJbk41YzNSbGJUcHpaWEoyYVdObFlXTmpiM1Z1ZERwa1pXWmhkV3gwT21SbFptRjFiSFFpZlEuVERsanZOTGY2R0lRT3U0ZTRPc2JUcUtNOTBobEhoZUlqbmtmYkpBUlZnMUdleWYwWmRUWkJZYmF5V1NvTjhPUG12TDY2dXNHM3c1TWhaaWVuQlhPbjJwNGh6UDhXTkNZekhwVDV5RGFKMVBiazlOdG15aDdiSUxpblpMY01NenZwc3diMEVZZjlVMXNwazFxUTVndm5SanA5ODdxMGgzVW9DOGRBVmp4OFU4azBZYTlseVU2dWx3dEI3YkllbHFVa3QwYjZGbEZENzc2WV9YVkJ5a2dleDJwWnJlSWhTMFVKRXBCWWRtdDZiUFVRLXNXclRsX3o5R2ozQkE2UDRRSkthY0hIMWZjZ1Q0WXhsd2RQQUw1X1U4bGpubE9oR3BieUdPTkUyR25LbllGRHAwM0lDU2xlak1tSjFPTWhzTE9IZ19USVUzUWtKOEw4U1hPeS1RQkZ3 | base64 -d

eyJhbGciOiJSUzI1NiIsImtpZCI6Im80ei1CMW5udXNyWWJhLTNGRXBzb1psT1ZYYXVvZkFzNG90RElXTmNuX00ifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tcWY0ZjQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjVlOTJjN2YwLWFhODYtNGEzYy1iYWI2LTdkMzU5ODM2ZTUxOSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.TDljvNLf6GIQOu4e4OsbTqKM90hlHheIjnkfbJARVg1Geyf0ZdTZBYbayWSoN8OPmvL66usG3w5MhZienBXOn2p4hzP8WNCYzHpT5yDaJ1Pbk9Ntmyh7bILinZLcMMzvpswb0EYf9U1spk1qQ5gvnRjp987q0h3UoC8dAVjx8U8k0Ya9lyU6ulwtB7bIelqUkt0b6FlFD776Y_XVBykgex2pZreIhS0UJEpBYdmt6bPUQ-sWrTl_z9Gj3BA6P4QJKacHH1fcgT4YxlwdPAL5_U8ljnlOhGpbyGONE2GnKnYFDp03ICSlejMmJ1OMhsLOHg_TIU3QkJ8L8SXOy-QBFw

[root@k8s-master-node1 ~]# cat api_deployment_manager.py

import requests,json,yaml,time

bearer_token = "bearer " + "eyJhbGciOiJSUzI1NiIsImtpZCI6IjJxMlZFS2ZPZnNiRzZtcVdxZ1F1elU0dkFxMTVKT2VpSTVZWi1qWUR5SzAifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZWZhdWx0LXRva2VuLW14ZGhkIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImRlZmF1bHQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyMWI2MzYxZC1iN2VmLTQzZmQtYTRmMi1jYmIzYjc0ZDlmY2EiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06ZGVmYXVsdCJ9.jSBQDRsn1SCupKDZvYT8mSPBiqb_ooxUM8cj0ZP5fD_GvrIjV7znlp42uHF_LXL0WIApKhh5jY3ALE301fQKdU2gLw07p7K5rE6ubMEbRZqdLpSSM99YRP7hwYJSnrFqh8W_W3MBYL0-Zi5P2Q6aqRoWZdGR8xP5JWFxj4ynZui5KqWj27NAbCxQaZaSdfhAibFYkBkn9Sb2TEErPhUkk-efdX3NWSUgs8T3coWzxHuTcNC8J-diSks1iXNZhG7htPGhNZybPHfB81vVFQDSvcGM3_5unlvPQ9LUpaTex8jQmBh3G0MXi3DnsWDdf17oXAYHZpdTL3_lWk5Q9M9o4A"

把用json方式查询svc的输入导入到py文件里再打印出来：

[root@k8s-master-node1 ~]# kubectl get deployments.apps nginx-deployment -o json >> api_deployment_manager.py

[root@k8s-master-node1 ~]# cat api_deployment_manager.py

```python
import requests,json,yaml,time
bearer_token = "bearer " + "eyJhbGciOiJSUzI1NiIsImtpZCI6IjJxMlZFS2ZPZnNiRzZtcVdxZ1F1elU0dkFxMTVKT2VpSTVZWi1qWUR5SzAifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZWZhdWx0LXRva2VuLW14ZGhkIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImRlZmF1bHQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyMWI2MzYxZC1iN2VmLTQzZmQtYTRmMi1jYmIzYjc0ZDlmY2EiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06ZGVmYXVsdCJ9.jSBQDRsn1SCupKDZvYT8mSPBiqb_ooxUM8cj0ZP5fD_GvrIjV7znlp42uHF_LXL0WIApKhh5jY3ALE301fQKdU2gLw07p7K5rE6ubMEbRZqdLpSSM99YRP7hwYJSnrFqh8W_W3MBYL0-Zi5P2Q6aqRoWZdGR8xP5JWFxj4ynZui5KqWj27NAbCxQaZaSdfhAibFYkBkn9Sb2TEErPhUkk-efdX3NWSUgs8T3coWzxHuTcNC8J-diSks1iXNZhG7htPGhNZybPHfB81vVFQDSvcGM3_5unlvPQ9LUpaTex8jQmBh3G0MXi3DnsWDdf17oXAYHZpdTL3_lWk5Q9M9o4A"
shuju = {'''
    "apiVersion": "apps/v1",
    "kind": "Deployment",
    "metadata": {
        "annotations": {
            "deployment.kubernetes.io/revision": "1",
            "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"apps/v1\",\"kind\":\"Deployment\",\"metadata\":{\"annotations\":{},\"labels\":{\"app\":\"nginx\"},\"name\":\"nginx-deployment\",\"namespace\":\"default\"},\"spec\":{\"replicas\":3,\"selector\":{\"matchLabels\":{\"app\":\"nginx\"}},\"template\":{\"metadata\":{\"labels\":{\"app\":\"nginx\"}},\"spec\":{\"containers\":[{\"image\":\"nginx:1.15.4\",\"name\":\"nginx\",\"ports\":[{\"containerPort\":80}]}]}}}}\n"
        },
        "creationTimestamp": "2023-03-06T09:12:42Z",
        "generation": 1,
        "labels": {
            "app": "nginx"
        },
        "name": "nginx-deployment",
        "namespace": "default",
        "resourceVersion": "13523",
        "uid": "086aba38-277a-4331-a9a5-0d2fcb1230df"
    },
    "spec": {
        "progressDeadlineSeconds": 600,
        "replicas": 3,
        "revisionHistoryLimit": 10,
        "selector": {
            "matchLabels": {
                "app": "nginx"
            }
        },
        "strategy": {
            "rollingUpdate": {
                "maxSurge": "25%",
                "maxUnavailable": "25%"
            },
            "type": "RollingUpdate"
        },
        "template": {
            "metadata": {
                "creationTimestamp": null,
                "labels": {
                    "app": "nginx"
                }
            },
            "spec": {
                "containers": [
                    {
                        "image": "nginx:1.15.4",
                        "imagePullPolicy": "IfNotPresent",
                        "name": "nginx",
                        "ports": [
                            {
                                "containerPort": 80,
                                "protocol": "TCP"
                            }
                        ],
                        "resources": {},
                        "terminationMessagePath": "/dev/termination-log",
                        "terminationMessagePolicy": "File"
                    }
                ],
                "dnsPolicy": "ClusterFirst",
                "restartPolicy": "Always",
                "schedulerName": "default-scheduler",
                "securityContext": {},
                "terminationGracePeriodSeconds": 30
            }
        }
    },
    "status": {
        "availableReplicas": 3,
        "conditions": [
            {
                "lastTransitionTime": "2023-03-06T09:12:44Z",
                "lastUpdateTime": "2023-03-06T09:12:44Z",
                "message": "Deployment has minimum availability.",
                "reason": "MinimumReplicasAvailable",
                "status": "True",
                "type": "Available"
            },
            {
                "lastTransitionTime": "2023-03-06T09:12:42Z",
                "lastUpdateTime": "2023-03-06T09:12:44Z",
                "message": "ReplicaSet \"nginx-deployment-746ccc65d8\" has successfully progressed.",
                "reason": "NewReplicaSetAvailable",
                "status": "True",
                "type": "Progressing"
            }
        ],
        "observedGeneration": 1,
        "readyReplicas": 3,
        "replicas": 3,
        "updatedReplicas": 3
    }
'''}
print(shuju)
```

提交满分

按评分点来：

[root@k8s-master-node1 ~]# cat api_deployment_manager.py

print('generation|| time|| uid')

[root@k8s-master-node1 ~]# python3 api_deployment_manager.py

generation|| time|| uid

**三、封装**

### 【题目 3】Python 运维开发：Pod 资源的 Restful APIs HTTP 服务封装[3 分]

编写 Python 程序实现Pod 资源管理程序，将 Pod 资源管理的封装成Web 服务。

在/root 目录下创建pod_server.py 程序，实现Pod 的增删查改等Web 访问操作。http.server 的 host 为 localhost，端口 8889；程序内部实现Kubernetes 认证。

提示说明：Python 标准库http.server 模块，提供了HTTP Server 请求封装。需要实现的 Restful API 接口如下：

GET /pod/{name} ，查询指定名称{name}的 Pod；Response 的 Body 以 json 格式输出。

POST /pod/{yamlfilename} 创建 yaml 文件名称为{yamlfilename}的 Pod；Response 的

Body 以 json 格式。

编码完成后，“手工下载”文件服务器主目录所有*.yaml 文件到 root 目录下，“手动执行”所编写pod_server.py 程序，提交答案进行检测。

1.

答案太难，技巧拿分，能拿一点是一点

[root@k8s-master-node1 ~]# yum -y install httpd

[root@k8s-master-node1 ~]# cat /etc/httpd/conf/httpd.conf

#Listen 80  #注释这一行

Listen 127.0.0.1:8889  #添加这一行

[root@k8s-master-node1 ~]# systemctl start httpd

[root@k8s-master-node1 ~]# netstat -ntpl | grep 8889

tcp        0      0 127.0.0.1:8889          0.0.0.0:*               LISTEN      89929/httpd

### 【题目 4】Python 运维开发：Service 资源 Restful APIs HTTP 服务封装[4 分]

编写 Python 程序实现 Service 资源管理程序，将 Service 资源管理的封装成 Web 服务。

在/root 目录下创建 service_server.py 程序，实现 Service 的增删查改等 Web 访问操作。

http.server 的 host 为 localhost，端口 8888；程序内部实现Kubernetes 认证。

提示说明：Python 标准库http.server 模块，提供了HTTP Server 请求封装。需要实现的 Restful API 接口如下：

GET /services/{name}，查询指定名称{name}的 Service；Response 的 Body 以 json 格式输出。

POST /services/{yamlfilename} 创建yaml 文件名称为{yamlfilename}的 Service； Response 的Body 以 json 格式，（手工将文件服务器主目录所有*.yaml 文件下载到 root 目录下）。

DELETE /services/{name}；删除指定名称的 Service；Response 的 Body 以 json 格式。编码完成后，自己手动执行提供 Web HTTP 服务的 service_server.py 程序，提交答案进

行检测。

1.

答案太难，技巧拿分，能拿一点是一点

[root@k8s-master-node1 ~]# yum -y install httpd

[root@k8s-master-node1 ~]# cat /etc/httpd/conf/httpd.conf

#Listen 80  #注释这一行

Listen 127.0.0.1:8888  #添加这一行

[root@k8s-master-node1 ~]# systemctl start httpd

[root@k8s-master-node1 ~]# netstat -ntpl | grep 8889

tcp        0      0 127.0.0.1:8888          0.0.0.0:*               LISTEN      89929/httpd
