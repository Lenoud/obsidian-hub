# 基于Kubernetes+GitLab+Drone构建CICD

基于Kubernetes+GitLab+Drone构建CICD相关技术笔记。

Kubernetes 容器编排相关笔记。

基于Kubernetes+Gitlab+Drone CI

持续集成

**1 案例目标**

（1）掌握Drone的使用和管理。

（2）掌握Gitlab的使用和管理。

（3）了解CICD的配置步骤和方法。

**2 案例准备**

**2.1 规划节点**

节点规划，见表2-1-1。

表2-1-1节点规划

| **IP** | **主机名** | **节点** |
| :---: | :---: | :---: |
| 10.24.2.5 | master | master节点、Harbor仓库节点 |
| 10.24.2.6 | node | node节点 |

**2.2 基础准备**

Kubernetes集群已部署完成。

**3 案例实施**

**3.1 部署GitLab**

#### （1）导入镜像
所有节点解压软件包并导入镜像：

# tar -zxvf Drone.tar

[root@k8s-master-node1 ~]# ll Drone

total 4543276

-rw-r--r--. 1 root root     127799 Feb 21 10:54 demo-2048.tar.gz

-rw-r--r--. 1 root root 4652179968 Feb 16 09:46 image.tar

# docker load -i Drone/image.tar

#### （2）部署GitLab
编写GitLab YAML文件：

[root@k8s-master-node1 ~]# vi gitlab-deployment.yaml

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: devops
---
apiVersion: v1
kind: Service
metadata:
  name: gitlab
  namespace: devops
spec:
  type: NodePort
  ports:
  - port: 443
    targetPort: 443
    name: gitlab-443
  - port: 80
    nodePort: 30888
    targetPort: 80
    name: gitlab-80
  - port: 22
    targetPort: 22
    name: gitlab-22
  selector:
    app: gitlab
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: gitlab
  namespace: devops
spec:
  selector:
    matchLabels:
      app: gitlab
  revisionHistoryLimit: 2
  template:
    metadata:
      labels:
        app: gitlab
    spec:
      containers:
      - image: gitlab/gitlab-ce
        name: gitlab
        imagePullPolicy: IfNotPresent
        env:
        - name: GITLAB_ROOT_PASSWORD  # 设置GitLab密码
          value: P@ssw0rd
        - name: GITLAB_ROOT_EMAIL
          value: guorui@example.com
        ports:
        - containerPort: 443
          name: gitlab-443
        - containerPort: 80
          name: gitlab-80
        - containerPort: 22
          name: gitlab-22
```

部署GitLab：

[root@k8s-master-node1 ~]# kubectl apply -f gitlab-deployment.yaml

namespace/devops created

service/gitlab created

deployment.apps/gitlab created

查看Pod：

[root@k8s-master-node1 ~]# kubectl -n devops get pods

NAME                 READY   STATUS    RESTARTS   AGE

gitlab-5475ccb89d-kz97c   1/1       Running     0           41s

#### （3）登录GitLab
查看Service：

[root@k8s-master-node1 ~]# kubectl -n devops get svc

NAME     TYPE     CLUSTER-IP     EXTERNAL-IP     PORT(S)     AGE

gitlab   NodePort   10.96.33.231   <none>        443:46787/TCP,80:30888/TCP,22:13813/TCP   106s

GitLab启动较慢，等待5-10分钟左右，在浏览器上通过http://master_IP:30888访问GitLab，如图所示：

![[1686464888431-3215c7dd-d994-4da4-a07b-1db690f5a6ae.png]]

输入用户名和密码进行登录，登录成功后如图所示：

![[1686464888756-581d8c70-2158-40bc-99da-58de396cdd46.png]]

#### （4）配置GitLab
登录GitLab，点击用户头像区域的倒三角按钮，然后点击“Edit profile”进入User Settings界面，如图所示：

![[1686464889025-0e2de7e6-a49c-4b00-bab6-4858428d7326.png]]

点击左侧导航栏“Applications”，创建一个GitLab OAuth应用程序，名字为drone，Redirect URL为Drone登录地址，并勾选api和read_user，如图所示：

看清地址端口号：30839  //drone的端口  http://192.168.100.40:30839/login

![[1686464889321-9f0adf7f-dfa8-40e7-8b3b-ac1266e66cb3.png]]

配置完成后保存退出，如图所示：

![[1686464889598-caea5192-a434-4bc1-afff-0bc837dbd8a3.png]]

将Application ID和Secret的值拷贝到文本中：

[root@k8s-master-node1 ~]# vi secret.yaml

94cd2ae492215b93c46af81c6ffb2c4dd342559a12563b31f83a1eec93e1a16f

091c41be2b098aed570a5ce02360626323d806b5dabcadc5a209ea481a2ac5f1

登录GitLab管理员界面（	http://master_IP:30888/admin），如图所示：

![[1686464889877-7ad87d43-f9c9-4625-90e6-2afa4e46f22f.png]]

依次点击“Settings”→“Network”→“Outbound requests”，勾选“Allow requests to the local network from web hooks and services”，如图所示：

![[1686464890257-a218be7a-fa81-4c73-89ff-541411919d0f.png]]

点击“Save changes”保存退出。

#### （5）导入项目到GitLab
登录GitLab首页，如图所示：

![[1686464890520-76e34793-7be1-4977-8452-6d2ee8ebb25f.png]]

点击“New project”，如图所示：

![[1686464890805-98e5d0c8-0277-4ac2-906d-fd7623467ac9.png]]

选择“Import project”，如图所示：

![[1686464891163-b41fa038-6efa-4e3d-82a2-318cad341d3d.png]]

点击“GitLab export”，然后在“Project name”和“Project slug”处输入“demo-2048”，如图所示：

![[1686464891460-7b886096-a45b-4d1f-abdb-a6593de34ad3.png]]

点击“浏览”，选择提供的软件包demo-2048.tar.gz，如图所示：

![[1686464891952-97742775-8d74-40be-b84c-a4956ded71fc.png]]

点击“Import project”导入项目，如图所示：

![[1686464892294-a3f11c6a-9c39-417f-b4ac-0deae8ed5d26.png]]

**3.2 部署Drone**

#### （1）部署Drone
创建共享秘钥：

[root@k8s-master-node1 ~]# openssl rand -hex 16c

6b41c3a68dd5db7066a7ee419b389cf4

编写Drone YAML文件：

[root@k8s-master-node1 ~]# vi drone-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: drone-server
  namespace: devops
  labels:
    app: drone
    type: server
spec:
  replicas: 1
  selector:
    matchLabels:
      app: drone
      type: server
  template:
    metadata:
      namespace: drones
      creationTimestamp: null
      labels:
        app: drone
        type: server
    spec:
      containers:
        - name: drone-server
          image: 'drone/drone:1.10.1'
          ports:
            - containerPort: 80
              protocol: TCP
          env:
            - name: DRONE_GITLAB_CLIENT_ID  # 前面记录的Gitlab Application ID值
              value: 94cd2ae492215b93c46af81c6ffb2c4dd342559a12563b31f83a1eec93e1a16f
            - name: DRONE_GITLAB_CLIENT_SECRET  # 前面记录的Gitlab Secret值
              value: 091c41be2b098aed570a5ce02360626323d806b5dabcadc5a209ea481a2ac5f1
            - name: DRONE_RPC_SECRET  # 使用openssl rand -hex 16 创建的一个共享密钥，以验证runner与drone-server之间的通信
              value: 6b41c3a68dd5db7066a7ee419b389cf4
            - name: DRONE_GITLAB_SERVER  # GitLab服务地址
              value: 'http://192.168.100.40:30888'
            - name: DRONE_SERVER_HOST  # Drone服务地址
              value: '192.168.100.40:30839'
            - name: DRONE_SERVER_PROTO  # drone-server协议，设置http或https
              value: http
            - name: DRONE_GITLAB  # 启用GitLab
              value: 'true'
            - name: DRONE_USER_CREATE  # 授权管理员权限
              value: 'username:root,admin:true'
            - name: DRONE_AGENTS_ENABLED  # 启用身份验证
              value: 'true'
            - name: DRONE_LOGS_TRACE  # 启用日志
              value: 'true'
          imagePullPolicy: IfNotPresent
      restartPolicy: Always
      imagePullSecrets:
        - name: harbor
---
# The service
apiVersion: v1
kind: Service
metadata:
  name: drone-server-service
  namespace: devops
spec:
  type: NodePort
  selector:
    app: drone
    type: server
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30839
```

部署Drone：

[root@k8s-master-node1 ~]# kubectl apply -f drone-deployment.yaml

deployment.apps/drone-server created

service/drone-server-service created

查看Pod：

[root@k8s-master-node1 ~]# kubectl -n devops get pods

NAME                       READY   STATUS    RESTARTS   AGE

drone-server-6dcdd9f965-xksh2   1/1        Running    0            28s

gitlab-5475ccb89d-kz97c         1/1        Running    0            29m

#### （2）部署Drone Runner
编写Drone Runner YAML文件：

[root@k8s-master-node1 ~]# vi drone-runners.yaml

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: drone
  namespace: devops
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: devops
  name: drone
rules:
- apiGroups:
  - ""
  resources:
  - secrets
  verbs:
  - create
  - delete
- apiGroups:
  - ""
  resources:
  - pods
  - pods/log
  verbs:
  - get
  - create
  - delete
  - list
  - watch
  - update

---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: drone
  namespace: devops
subjects:
- kind: ServiceAccount
  name: drone
  namespace: devops
roleRef:
  kind: Role
  name: drone
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: drone-runner
  namespace: devops
  labels:
    app.kubernetes.io/name: drone
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: drone
  template:
    metadata:
      labels:
        app.kubernetes.io/name: drone
    spec:
      serviceAccountName: drone
      serviceAccount: drone
      containers:
      - name: runner
        image: drone/drone-runner-kube:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 3000
        env:
        - name: DRONE_RPC_HOST  # Drone服务地址
          value: '10.24.2.5:30839'
        - name: DRONE_RPC_PROTO  # Drone服务协议
          value: http
        - name: DRONE_RPC_SECRET # 使用openssl rand -hex 16 创建的一个共享密钥，以验证Runner与Drone之间的通信
          value: 6b41c3a68dd5db7066a7ee419b389cf4
```

安装Drone Runner：

[root@k8s-master-node1 ~]# kubectl apply -f drone-runners.yaml

serviceaccount/drone created

role.rbac.authorization.k8s.io/drone created

rolebinding.rbac.authorization.k8s.io/drone created

deployment.apps/drone-runner created

查看Pod：

[root@k8s-master-node1 ~]# kubectl -n devops get pods

NAME                      READY   STATUS    RESTARTS   AGE

drone-runner-567dd5cfcf-sjmcl   1/1       Running     0           28s

drone-server-6dcdd9f965-xksh2   1/1       Running     0           11m

gitlab-5475ccb89d-kz97c         1/1       Running     0           39m

#### （3）访问并配置Drone
查看Service：

[root@k8s-master-node1 ~]# kubectl -n devops get svc

NAME      TYPE      CLUSTER-IP      EXTERNAL-IP     PORT(S)     AGE

drone-server-service    NodePort   10.96.137.89   <none>     80:30839/TCP    12m

在浏览器上通过http://master_IP:30839访问Drone，系统将自动重定向到GitLab进行登录，如图所示：

#我实验到这一步提示The redirect URI included is not valid.  #一般是上面的gitlab-GUI配置时地址写错造成的

![[1686464892715-0bb955a0-8466-4a83-86dd-73b32a718d12.png]]

![[1686464893043-9acb9b25-5070-4559-97ee-25be0d528fdf.png]]

点击Authorize，如图所示：

![[1686464893404-4ae85150-bf59-4ea2-a4d4-a5f125619493.png]]

点击“/root/demo-2048”存储库后的“ACTIVATE”按钮，如图所示：

![[1686464893693-ea74c5e8-ceeb-4017-86f9-a499a831fb5b.png]]

点击“ACTIVATE REPOSTIORY”启用存储库，如图所示：

![[1686464894009-7312b68a-da30-493b-8c2a-32124639ed42.png]]

勾选“Trusted”选项，然后点击“SAVE”保存。

#### （4）添加环境变量
创建一个拥有集群权限的SA账户：

[root@k8s-master-node1 ~]# vi admin.yaml

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: devops-admin
  namespace: kube-system
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: devops-admin
subjects:
  - kind: ServiceAccount
    name: devops-admin
    namespace: kube-system
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

[root@k8s-master-node1 ~]# kubectl apply -f admin.yaml

serviceaccount/devops-admin created

clusterrolebinding.rbac.authorization.k8s.io/devops-admin created

获取该用户的Token：

[root@k8s-master-node1 ~]# kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep devops-admin | awk '{print $1}')

Name:         devops-admin-token-crtsz

Namespace:    kube-system

Labels:       <none>

Annotations:  kubernetes.io/service-account.name: devops-admin

              kubernetes.io/service-account.uid: 0565e0e7-42aa-4931-b5f0-9cbb00f1fb2f

Type:  kubernetes.io/service-account-token

Data

====

token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Ijl3cjhmQWk2ZWgyaXQ2eEQtQkVGclpOX2luSzVZZDRvaWlRdl9UNTlyRXMifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZXZvcHMtYWRtaW4tdG9rZW4tY3J0c3oiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGV2b3BzLWFkbWluIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiMDU2NWUwZTctNDJhYS00OTMxLWI1ZjAtOWNiYjAwZjFmYjJmIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOmRldm9wcy1hZG1pbiJ9.mz4RDUWhMtuoa1RG6B2AxQ2LztQtHQtRfluzC3FETkqSQF8wPT0GmP1KSbQ6oHcCVWSmBtlrUKAWLyHSNWkW7SxlAd1Gx0czloz379h-QUJ4iQFnrFvHvOFFCZ1UjiYgwyoam7qI_fRsJtDs8PDz1gqDMArvFIlsMzz81_D2Je2cvAwRN-Xqa3YKL_lIjyjO7hcNAc0LsM1pFs5lJAvca0OhbT41CWYwIs-FR6c-0NhF_OVDJbXElsx6PNetcTPY7--1jb97_eRX51Ty5usaj8W_VeRLN5G0n5g6d6RU2JitKcx8JEgosSiQJWRt5IIASUzbCs773eQ7tW0rVCel1w

ca.crt:     1099 bytes

namespace:  11 bytes

获取Kubernetes CA证书的Base-64编码的字符串

[root@k8s-master-node1 ~]# base64 /etc/kubernetes/pki/ca.crt

LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMvakNDQWVhZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJeU1ESXhOREEzTWpJME5Gb1hEVE15TURJeE1qQTNNakkwTkZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTVpOCkk3T3h3N2E5Y3NGdXpacmc0amwzZzEvM01Bc0IvUnFTRTZpSzFQTGdwbDN5aEZib3lWb3BCWFo4VG5xbHU3eEgKYVpmNHF2UkhvMC9kVmh6aGE2TFY3cTcvKzlCKzRFb2J3bnFlcnE1TlZ3OThNRjBvc2RocEVsTDFxWkhDeC9ocAprb1BoYThMTWZNbzNIbTFRQmJiOUZEWXg5eCs3bkdoNmV6SjR5UThFc2d5OTBrNXRNSDR6eUgwMXhnRjQ0UzZaCkpvSHdaaElOTnh2VFRTeUxSVVBETWwvYWVjajBadlowRDJVN3RRdEN2QTBmd0VNS2tBSXJGUnlTZGIxczRHT3oKUG5HUnVqU2x5QWRiM3VxaEl1ZWRuRmFwRzBHa2ljZ1Z0aDVQSkFiWFpzZlpZT25MdmJMS0ZLaTV0K010cjlESApxeWo3c2l2cDNyb0JHUytVVzdFQ0F3RUFBYU5aTUZjd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0hRWURWUjBPQkJZRUZJYlVPWXBKNzUveWd4QU5NNGR3bm1veEdwSHhNQlVHQTFVZEVRUU8KTUF5Q0NtdDFZbVZ5Ym1WMFpYTXdEUVlKS29aSWh2Y05BUUVMQlFBRGdnRUJBSjJYOG9jR1lPcnI4ZUN2Z244Nwp4b2RrTElDcmZBNUxWemFwM2VoWmdHZWFKY3lJQzlyK2F1RVNwZzBDOHZNanoydmw5bDRLbURFY1piSFFkTGhzCm1IUUZva25EUFBlbTU4eGNIaG9ZMmxhcm5aQmxmQlFEdUgrYVRRQjg2MTBYUHZNN1Z5NFBTVFk0dGszZnJJVHIKZDE1Zlp3NlBLM3RXWlBqdHVhQ0ZOc1FMWW5zU2JWbWIwWTVUbE1kdjVtSXdScnEwdk41aGJaN29EcVE5Q0hhbwp6b0Q0UUdUQ2g1RTJuQllrOVRGd3JvRFExcEhTSWtBWHRweVRrR0pLQkNYanlNUFZNTVJyc3dQek1wa0lzMmg4Ckp3N1Q5WXhOak9lR0hkd3BOcXdSRmFjaUd0cDhWNEdFVWFNeVhpMExUbmw0NW9CVWpqL3VqWHZWL1VzWjNqMzAKMzlJPQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==

在Secrets处配置k8s_token、k8s_ca、harbor_username和harbor_password四个环境变量。

添加环境变量k8s_token，其值为获取的token值，如图所示：

![[1686464894245-d26d9912-483f-4a88-98f1-53b8194da5c2.png]]

添加环境变量k8s_ca，其值为Kubernetes CA证书进行Base-64编码后的值，如图所示：

![[1686464894605-8a2593bd-edad-4e20-9655-c34d0777f6e0.png]]

继续添加环境变量harbor_username（值为admin）和harbor_password（值为Harbor12345），添加完成后如图所示：

![[1686464894860-5a6b966b-2c17-441d-b81b-c4135bd8f4f6.png]]

**3.3 Drone CI**

#### （1）集群中设置映射
查看GitLab Pod的IP：

[root@k8s-master-node1 ~]# kubectl -n devops get pods -owide

NAME    READY    STATUS    RESTARTS    AGE    IP    NODE    NOMINATED NODE    READINESS GATES

drone-runner-f986d69fb-stm2x    1/1     Running   0          40m   10.244.1.62   k8s-worker-node1   <none>           <none>

drone-server-55d5786864-jxmjb   1/1     Running   0          40m   10.244.1.61   k8s-worker-node1   <none>           <none>

gitlab-88d5cb9c8-k5zt8          1/1     Running   0          68m   10.244.1.59   k8s-worker-node1   <none>           <none>

在集群中自定义hosts添加gitlab Pod的解析：

[root@k8s-master-node1 ~]# kubectl edit configmap coredns -n kube-system

# Please edit the object below. Lines beginning with a '#' will be ignored,

# and an empty file will abort the edit. If an error occurs while saving this file will be

# reopened with the relevant failures.

#

apiVersion: v1

data:

  Corefile: |

    .:53 {

        errors

        health {

           lameduck 5s

        }

        ready

        kubernetes cluster.local in-addr.arpa ip6.arpa {

           pods insecure

           fallthrough in-addr.arpa ip6.arpa

           ttl 30

        }

添加 #注意缩进一定要一致

        hosts {

            10.244.1.59 gitlab-88d5cb9c8-k5zt8

            fallthrough

        }

        prometheus :9153

        forward . /etc/resolv.conf {

           max_concurrent 1000

        }

        cache 30

        loop

        reload

        loadbalance

    }

kind: ConfigMap

metadata:

  creationTimestamp: "2022-01-28T02:12:16Z"

  name: coredns

  namespace: kube-system

  resourceVersion: "278"

  uid: 630f69e2-5254-4813-b1af-4ae2a532d8e1

[root@k8s-master-node1 ~]# kubectl -n kube-system rollout restart deployment coredns

deployment.apps/coredns restarted

#### （2）Harbor配置
```bash
docker login -u admin -p "Harbor12345" 192.168.100.40
```

登录Harbor仓库，创建一个公开项目devops：

![[1686464895138-70c8f03b-7cf2-4f07-8d31-e95690519cd9.png]]

将镜像tomcat:8.5.64-jdk8重新打标签并上传到Harbor仓库中

[root@k8s-master-node1 ~]# docker tag tomcat:8.5.64-jdk8 10.24.2.5/devops/tomcat:8.5.64-jdk8

[root@k8s-master-node1 ~]# docker push 10.24.2.5/devops/tomcat:8.5.64-jdk8

#### （3）触发构建
登录GitLab并进入项目demo-2048，选择分支drone，如图所示：

![[1686464895417-474886c7-2d6f-4fd9-b820-6acf480c96d2.png]]

点击Dockerfiles，然后点击Dockerfile，如图所示：

![[1686464895726-0b61cbc7-6408-4dc7-9bf0-ffb832de8d62.png]]

点击Edit，将基础镜像修改为10.24.2.5/devops/tomcat:8.5.64-jdk8，如图所示：

![[1686464896048-68a13504-d7fe-424e-a7a7-acfe6f7f2475.png]]

修改完成后点击“Commit changes”保存。

返回drone分支，将template下的[demo-2048.yaml](http://10.24.2.36:8888/root/demo-2048/-/blob/drone/template/demo-2048.yaml)模板修改为以下内容：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-2048
  namespace: devops
  labels:
    app: demo-2048
    name: demo-2048
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo-2048
      name: demo-2048
  template:
    metadata:
      labels:
        app: demo-2048
        name: demo-2048
    spec:
      containers:
        - name: demo-2048
          image: >-
            10.24.2.5/devops/demo-2048:{{build_tag}}
          ports:
            - containerPort: 8080
              name: demo8889
              protocol: TCP
          resources:
            limits:
              cpu: 128m
              memory: 256Mi
            requests:
              cpu: 128m
              memory: 128Mi
          imagePullPolicy: IfNotPresent
      restartPolicy: Always
      imagePullSecrets:
        - name: harbor
```

返回drone分支，编写流水线脚本.[drone.yml](http://10.24.2.14:8888/root/demo-2048/-/blob/drone/.drone.yml)：

```yaml
kind: pipeline
type: kubernetes
name: demo-2048
# 跳过验证证书，根据实际情况要或不要，github不需要
clone:
  skip_verify: true
# 选择pipeline跑在demo命名空间里
metadata:
  namespace: devops

# node选择
node_selector:
  kubernetes.io/hostname: k8s-master-node1

# 挂载本地目录
volumes:
- name: cache
  host:
    path: /data/cache

# 第一阶段：构建代码
steps:
  - name: build code
    image: maven:3.6-jdk-8
    # 在容器里执行的shell命令列表
    commands:
    - mvn clean install -DskipTests=true
    - cd target && jar -xf 2048.war
    - ls -l

# 第二阶段: 构建镜像&推送镜像
  - name: build image & push
    image: plugins/docker     # docker插件
    settings:
      # 镜像仓库组名称
      repo: 10.24.2.5/devops/demo-2048
      # 镜像仓库名称
      registry: 10.24.2.5
      # 在web界面中设置的密钥
      username:
        from_secret: harbor_username
      password:
        from_secret: harbor_password
      # 镜像tag定义
      tags:
        - ${DRONE_BRANCH}-${DRONE_COMMIT}-${DRONE_BUILD_NUMBER}
      # 启用非ssl加密
      insecure: true
      # 构建镜像的dockerfile文件
      dockerfile: ./Dockerfiles/Dockerfile

# 第三阶段: 服务滚动更新
  - name: k8s-service
    # Kubernetes插件
    image: danielgormly/drone-plugin-kube:0.2.0
    settings:
      build_tag: ${DRONE_BRANCH}-${DRONE_COMMIT}-${DRONE_BUILD_NUMBER}
      # yaml模板
      template: template/demo-2048.yaml
      # apiserver地址
      server: https://10.24.2.5:6443
      # Kubernetes服务帐户令牌 'kubectl get secret -nkube-system|grep token'
      token:
        from_secret: k8s_token
      # K8s CA证书的Base-64编码的字符串
      ca:
        from_secret: k8s_ca
# 第四阶段: 创建service
  - name: k8s-deploy
    # Kubernetes插件
    image: danielgormly/drone-plugin-kube:0.2.0
    settings:
      build_tag: ${DRONE_BRANCH}-${DRONE_COMMIT}-${DRONE_BUILD_NUMBER}
      # yaml模板
      template: template/service.yaml
      # apiserver地址
      server: https://10.24.2.5:6443
      # Kubernetes服务帐户令牌 'kubectl get secret -nkube-system|grep token'
      token:
        from_secret: k8s_token
      # K8s CA证书的Base-64编码的字符串
      ca:
        from_secret: k8s_ca

# 通过分支限制pipeline执行
trigger:
  branch:
  - drone
```

添加完成后如图所示：

![[1686464896369-7d5d9d2d-2364-4df4-b7e5-6104aa19238a.png]]

#### （4）查看
登录http://master_IP:30839/root/demo-2048查看构建结果，如图所示：

![[1686464896663-c3e48708-c852-40f5-94a2-27985c4c0025.png]]

点击“#”查看构建详情，如图所示：

![[1686464896933-3afd1617-8e1a-4163-96f0-d79ea968e180.png]]

登录Harbor查看镜像，如图所示：

![[1686464897209-bea1a969-857f-4b67-af5a-ee8ee57bb75e.png]]

查看Service：

[root@k8s-master-node1 ~]# kubectl -n devops get svc

NAME    TYPE    CLUSTER-IP    EXTERNAL-IP    PORT(S)    AGE

demo-2048    NodePort    10.96.52.85    <none>    8080:8889/TCP    6m43s

drone-server-service   NodePort   10.96.25.226    <none>    80:30839/TCP    110m

gitlab  NodePort  10.96.205.252  <none>   443:9777/TCP,80:30888/TCP,22:14963/TCP    123m

访问http:// master_IP:8889，如图所示：

![[1686464897476-601c043e-76f9-4569-9b1d-602a3990edbb.png]]

点击“New Game”，然后使用键盘方向键进行操控，如图所示：

![[1686464897743-ab3ca94c-d84f-4495-b549-329a7896297b.png]]

构建成功

![[1686464898024-ec595ee5-d698-4db2-abdb-de7d5a766655.png]]

![[1686464898319-51beed69-19f1-4dde-b1ba-05c86d49ee60.png]]
