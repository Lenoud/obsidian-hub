## 1.介绍
### 1.1 业务背景
> 本项目主要以现在非常火热主流的容器服务为主体，为解决企业在多人的团队开发中，管理团队成员和代码较为繁琐复杂的问题，并且简化了运维人员部署上线服务的流程，极大化的加快了业务的开发。
>

### 1.2 业务场景
> 解释业务的服务场景和商业价值。
>

| **场景/商业模式** | **商业价值** |
| --- | --- |
| 标准服务 | 代码自托管平台，极大化的方便公司的代码管理和业务开发 |
| 个性化服务（本次范围） | 提供个性化定制和本地私有化部署 |

## 2. 产品概述
### 2.1 产品定位
> 在团队化的应用和服务开发的场景下，提供优秀的代码托管和应用自动部署上线能力，服务现代化互联网软件开发公司的一款产品。
>

本项目搭建一个kubernetes容器自动化运维平台，基于k8s以容器化的方式部署一个企业级的代码托管平台gitlab，并且实现cicd（持续集成、持续交付和持续部署。）的解决方案。

### 2.2 服务对象
企业中的软件开发人员，运维人员，测试人员，等软件开发业务项目组。

### 2.3 服务架构
![[1688631041553-2b456154-6345-417b-80ef-70c868edb14e.jpeg]]

### 2.4 k8s介绍

### 2.5 gitlab介绍

### 2.6 cicd理念
**CI 持续集成（Continuous Integration）**

协同开发是目前主流的开发方式，也就是多位开发人员可以同时处理同一个应用的不同模块或者功能。

但是，如果企业计划在同一天，将所有开发分支代码集成在一起，最终可能会花费很多时间和进行很多重复劳动，费事费力。因为代码冲突是难以避免的。

如果开发人员本地的环境和线上不一致的话，那么这个问题就更加复杂了。

持续集成（CI）可以帮助开发者更加方便地将代码更改合并到主分支。

一旦开发人员将改动的代码合并到主分支，系统就会通过自动构建应用，并运行不同级别的自动化测试（通常是单元测试和集成测试）来验证这些更改，确保这些更改没有对应用造成破坏。

如果自动化测试发现新代码和现有代码之间存在冲突，CI 可以更加轻松地快速修复这些错误。

**CD 持续交付（Continuous Delivery）**

CI 在完成了构建、单元测试和集成测试这些自动化流程后，持续交付可以自动把已验证的代码发布到企业自己的存储库。

持续交付旨在建立一个可随时将开发环境的功能部署到生产环境的代码库。

在持续交付过程中，每个步骤都涉及到了测试自动化和代码发布自动化。

在流程结束时，运维团队可以快速、轻松地将应用部署到生产环境中。

**CD 持续部署（Continuous Deployment）**

对于一个完整、成熟的 CI/CD 管道来说，最后的阶段是持续部署。

它是作为持续交付的延伸，持续部署可以自动将应用发布到生产环境。

实际上，持续部署意味着开发人员对应用的改动，在编写完成后的几分钟内就能及时生效（前提是它通过了自动化测试）。这更加便于运营团队持续接收和整合用户反馈。

总而言之，所有这些 CI/CD 的关联步骤，都极大地降低了应用的部署风险。

不过，由于还需要编写自动化测试以适应 CI/CD 管道中的各种测试和发布阶段，因此前期工作量还是很大的。

## 3.kubernetes搭建
### 3.1环境准备
| master | centos7.2009 x64 | 4G | 3cpu |
| --- | --- | --- | --- |
| worker | centos7.2009 x64 | 8G | 4cpu |
| worker | centos7.2009 x64 | 8G | 4cpu |

普通集群模式主机列表

| **IP** **地址** | **主机名** | **角色** |
| --- | --- | --- |
| 192.168.100.101 | k8s-maste-noder01 | master | etcd | worker |
| 192.168.100.102 | k8s-worker-node1 | worker |
| 192.168.100.103 | k8s-worker-node2 | worker |

认证：集群节点需**统一认证**，需使用同一用户名和密码，本次项目使用root用户密码为000000，实际生产环境应考虑安全方面相关问题。

运行：在预设的master节点执行。

### 3.2 安装部署工具
本次k8s部署通过github开源项目kubeeasy部署

```bash
wget https://github.com/kongyu666/kubeeasy/releases/download/v1.3.2/kubeeasy-v1.3.2.sh
mv kubeeasy-v1.3.2.sh /usr/bin/kubeeasy && chmod +x /usr/bin/kubeeasy
```

```bash
wget https://github.com/kongyu666/kubeeasy/releases/download/v1.3.2/centos-7-rpms.tar.gz
wget https://github.com/kongyu666/kubeeasy/releases/download/v1.3.2/kernel-rpms-v5.17.9.tar.gz
wget https://github.com/kongyu666/kubeeasy/releases/download/v1.3.2/kubeeasy-v1.3.2.tar.gz
```

### 3.3 集群安装依赖包
使用kubeeasy在集群中安装软件包，包含远程连接ssh pass等软件包

```bash
kubeeasy install depend \
--host 192.168.100.101-192.168.100.103 \
--user root \
--password 000000 \
--offline-file ./centos-7-rpms.tar.gz
```

![[1688638390704-14ff50c9-4a43-42b6-8ea3-361dcb25019e.png]]

### 3.4 基础ssh配置
使用kubeeasy在集群中创建ssh免秘钥，其中优化的ssh的连接以及配置了集群的免秘钥登录等

```bash
kubeeasy check ssh \
--host 192.168.100.101-192.168.100.103 \
--user root \
--password 000000
```

```bash
kubeeasy create ssh-keygen \
--master 192.168.100.101 \
--worker 192.168.100.102,192.168.100.103 \
--user root \
--password 000000
```

![[1688638412781-123870b1-827e-443f-a763-c0f90b4ef39f.png]]

### 3.5 集群升级系统内核
使用kubeeasy升级集群的内核，将其3.10的内核升级为5.14（建议升级内核）。脚本执行完后，会自动重启主机，以生效系统内核。

```bash
kubeeasy install upgrade-kernel \
--host 192.168.100.101-192.168.100.103 \
--user root \
--password 000000 \
--offline-file ./kernel-rpms-v5.17.9.tar.gz
```

![[1688638577635-ba89a066-79a4-406b-922c-6b0f0c2fec23.png]]

### 3.6 部署K8S集群
使用kubeeasy部署K8S集群，以下分为**普通集群**和**高可用集群**的安装方式，根据实际情况选择一种方案部署。安装后的K8S的版本为v1.21.3，使用docker作为容器运行时，网络组件使用calico网络，默认还安装kuboard图形化管理器和K8S存储类（openebs-hostpath），K8S的证书有效期为100年，且开启了证书轮换

如需更改K8S的Pod网段，修改--pod-cidr参数相应的值

```bash
kubeeasy install kubernetes \
   --master 192.168.100.101 \
   --worker 192.168.100.102,192.168.100.103 \
   --user root \
   --password 000000 \
   --version 1.21.3 \
   --pod-cidr 10.244.0.0/16 \
   --offline-file ./kubeeasy-v1.3.2.tar.gz
```

![[1688638955250-a471c6d5-557b-4523-a7fd-7de66e3503ab.png]]

![[1688639030800-584f1465-cd4e-4b55-ba60-10059c6f5bc1.png]]

### 3.7 部署harbor仓库
[搭建harbor私有仓库](https://www.yuque.com/u33971054/atri/uw5o4tinsv9tl1ih)

## 4.gitlab平台的搭建
### **4.1部署GitLab**
GitLab主要涉及到3个应用：Redis、Postgresql和Gitlab 核心程序。

#### （1）基础准备
所有节点解压软件包并导入镜像：

# tar -zxvf CICD-Runner.tar.gz

# docker load -i cicd-runner/images/image.tar

#### （2）部署GitLab服务
master	节点配置好nfs服务器做持久化存储pvc

```powershell
[root@k8s-master-node1 manifests]# cat /etc/exports
/root/nfs-data/gitlab-conf *(insecure,rw,sync,no_root_squash)
/root/nfs-data/gitlab-log *(insecure,rw,sync,no_root_squash)
/root/nfs-data/gitlab-data *(insecure,rw,sync,no_root_squash)
[root@k8s-master-node1 nfs-data]# exportfs -r  && exportfs
/root/nfs-data/gitlab-conf
                <world>
/root/nfs-data/gitlab-log
                <world>
/root/nfs-data/gitlab-data
                <world>
```

新建命名空间kube-ops，在该命名空间下部署GitLab，YAML文件如下：

[root@k8s-master-node1 ~]# cd cicd-runner/manifests/

[root@k8s-master-node1 manifests]# vi gitlab-deployment.yaml

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: kube-ops
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: gitlab
  namespace: kube-ops
  labels:
    name: gitlab
spec:
  selector:
    matchLabels:
      name: gitlab
  template:
    metadata:
      name: gitlab
      labels:
        name: gitlab
    spec:
      containers:
      - name: gitlab
        image: yidaoyun/gitlab-ce:v1.0
        imagePullPolicy: IfNotPresent
        env:
        - name: GITLAB_ROOT_PASSWORD
          value: 12345678
        - name: GITLAB_ROOT_EMAIL
          value: 351719672@qq.com
        - name: GITLAB_HOST
          value: 192.168.100.101  #masterIP
        - name: GITLAB_PORT
          value: "80"
        - name: GITLAB_SSH_PORT
          value: "22"
        ports:
        - name: http
          containerPort: 80
        - name: ssh
          containerPort: 22
        volumeMounts:
        - mountPath: /var/opt/gitlab
          name: gitlab-data
        - mountPath: /var/log/gitlab
          name: gitlab-log
        - mountPath: /etc/gitlab
          name: gitlab-conf
      volumes:
      - name: gitlab-data
        persistentVolumeClaim:
          claimName: gitlab-data
      - name: gitlab-conf
        persistentVolumeClaim:
          claimName: gitlab-conf
      - name: gitlab-log
        persistentVolumeClaim:
          claimName: gitlab-log
---
apiVersion: v1
kind: Service
metadata:
  name: gitlab
  namespace: kube-ops
  labels:
    name: gitlab
spec:
  type: NodePort
  ports:
    - name: http
      port: 80
      targetPort: http
      nodePort: 30880
    - name: ssh
      port: 22
      targetPort: ssh
      nodePort: 32222
  selector:
    name: gitlab
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: gitlab-pv
spec:
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 15Gi
  nfs:
    path: /root/nfs-data/gitlab-data
    server: 192.168.100.101
  persistentVolumeReclaimPolicy: Retain
  storageClassName: nfs
  volumeMode: Filesystem
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: gitlab-conf
spec:
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 2Gi
  nfs:
    path: /root/nfs-data/gitlab-conf
    server: 192.168.100.101
  persistentVolumeReclaimPolicy: Retain
  storageClassName: nfs
  volumeMode: Filesystem
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: gitlab-log
spec:
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 1Gi
  nfs:
    path: /root/nfs-data/gitlab-log
    server: 192.168.100.101
  persistentVolumeReclaimPolicy: Retain
  storageClassName: nfs
  volumeMode: Filesystem
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: gitlab-data
  namespace: kube-ops
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 15G
  storageClassName: nfs
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: gitlab-conf
  namespace: kube-ops
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 2G
  storageClassName: nfs
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: gitlab-log
  namespace: kube-ops
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1G
  storageClassName: nfs

```

**创建资源：**

```powershell
[root@k8s-master-node1 manifests]# kubectl apply -f gitlab-deployment.yaml
```

**查看Pod：**

[root@k8s-master-node1 manifests]# kubectl -n kube-ops get pods

NAME                      READY   STATUS    RESTARTS   AGE

gitlab-8656b798ff-z54sm          1/1       Running    0            3m23s

![[1688641599689-5eae3349-5497-4aaa-a9d3-8fb678c63534.png]]

#### （3）自定义hosts
查看GitLab Pod的IP地址：

[root@k8s-master-node1 manifests]# kubectl -n kube-ops get pods -owide

NAME    READY    STATUS    RESTARTS    AGE    IP    NODE    NOMINATED NODE    READINESS GATES

gitlab-8656b798ff-z54sm       1/1    Running    0    10m    10.244.1.87    k8s-worker-node1    <none>    <none>

在集群中自定义hosts添加gitlab Pod的解析：

```powershell
[root@k8s-master-node1 manifests]# kubectl edit configmap coredns -n kube-system
```

........

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

## 添加以下字段

        hosts { #有空格！！

            10.244.1.87 gitlab-8656b798ff-z54sm

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

........

[root@k8s-master-node1 manifests]# kubectl -n kube-system rollout restart deployment coredns

deployment.apps/coredns restarted

![[1688641556466-b4339b9c-5aa4-4240-9bd5-fa84b4791ccd.png]]

#### （4）访问GitLab
查看Service：

[root@k8s-master-node1 manifests]# kubectl -n kube-ops get svc

NAME     TYPE     CLUSTER-IP   EXTERNAL-IP  PORT(S)            AGE

gitlab      NodePort   10.96.0.38    <none>    80:30880/TCP,22:32222/TCP   2m2s

通过http://master_ip:30880访问GitLab，如图所示：

![[1688642260313-b9be8388-78a8-4f46-b584-87324e11274c.png]]

登录后：

![[1688642988504-30cba939-d659-4c91-bd6f-ac98d1ccb718.png]]

## 5.gitlab平台的配置
### 5.1 创建一个新的公开项目
![[1688643414773-e2c3d99a-5836-420b-aa32-28b38dcc4cbb.png]]

### 5.2 **部署Gitlab CI Runner**
#### （1）获取 Gitlab CI Register Token
登录GitLab管理界面(http://master_ip:30880/admin)，然后点击左侧菜单栏中的Runners，如图所示：

![[1688643686026-41e7190d-c8c6-492b-a395-c38733dd6b8a.png]]

可以看到该页面中有两个重要参数：Runner URL和Register Token，后期部署Runner时会用到这两个参数。

#### （2）编写Gitlab CI Runner资源清单文件
**首先，通过ConfigMap资源来传递Runner镜像所需的环境变量，**

[root@k8s-master-node1 manifests]# vi runner-configmap.yaml

```yaml
apiVersion: v1
data:
  REGISTER_NON_INTERACTIVE: "true" #修改
  REGISTER_LOCKED: "false"
  METRICS_SERVER: "0.0.0.0:9100"
  CI_SERVER_URL: "http://192.168.100.101:30880/"  #添加masterIP+port
  RUNNER_REQUEST_CONCURRENCY: "4" #修改
  RUNNER_EXECUTOR: "kubernetes"
  KUBERNETES_NAMESPACE: "kube-ops" #修改
  KUBERNETES_PRIVILEGED: "true"
  KUBERNETES_CPU_LIMIT: "1"
  KUBERNETES_CPU_REQUEST: "500m"
  KUBERNETES_MEMORY_LIMIT: "1Gi"
  KUBERNETES_SERVICE_CPU_LIMIT: "1"
  KUBERNETES_SERVICE_MEMORY_LIMIT: "1Gi"
  KUBERNETES_HELPER_CPU_LIMIT: "500m"
  KUBERNETES_HELPER_MEMORY_LIMIT: "100Mi"
  KUBERNETES_PULL_POLICY: "if-not-present"
  KUBERNETES_TERMINATIONGRACEPERIODSECONDS: "10"
  KUBERNETES_POLL_INTERVAL: "5"
  KUBERNETES_POLL_TIMEOUT: "360"
kind: ConfigMap
metadata:
  labels:
    app: gitlab-ci-runner
  name: gitlab-ci-runner-cm
  namespace: kube-ops
```

**编写一个用于注册、运行和取消注册Gitlab CI Runner的小脚本。只有当Pod正常通过 Kubernetes（TERM信号）终止时，才会触发转轮取消注册。如果强制终止Pod（SIGKILL信号），Runner将不会注销自身。必须手动完成对这种被杀死的Runner的清理，配置清单文件如下：**

[root@k8s-master-node1 manifests]# vi runner-scripts-configmap.yaml

```yaml
apiVersion: v1
data:
  run.sh: |
    #!/bin/bash
    unregister() {
        kill %1
        echo "Unregistering runner ${RUNNER_NAME} ..."
        /usr/bin/gitlab-ci-multi-runner unregister -t "$(/usr/bin/gitlab-ci-multi-runner list 2>&1 | tail -n1 | awk '{print $4}' | cut -d'=' -f2)" -n ${RUNNER_NAME}
        exit $?
    }
    trap 'unregister' EXIT HUP INT QUIT PIPE TERM
    echo "Registering runner ${RUNNER_NAME} ..."
    /usr/bin/gitlab-ci-multi-runner register -r ${GITLAB_CI_TOKEN}
    sed -i 's/^concurrent.*/concurrent = '"${RUNNER_REQUEST_CONCURRENCY}"'/' /home/gitlab-runner/.gitlab-runner/config.toml
    echo "Starting runner ${RUNNER_NAME} ..."
    /usr/bin/gitlab-ci-multi-runner run -n ${RUNNER_NAME} &
    wait
kind: ConfigMap
metadata:
  labels:
    app: gitlab-ci-runner
  name: gitlab-ci-runner-scripts
  namespace: kube-ops
```

**可以看到需要一个GITLAB_CI_TOKEN，然后使用Gitlab CI runner token来创建一个 Kubernetes secret对象。将token进行base64编码：**

[root@k8s-master-node1 manifests]# echo 7KFagx5yf_ksN1s7zFsa | base64 -w0

N0tGYWd4NXlmX2tzTjFzN3pGc2EK

**然后使用上面的token创建一个Secret对象：**

[root@k8s-master-node1 manifests]# vi runner-token-secret.yaml

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: gitlab-ci-token
  namespace: kube-ops
  labels:
    app: gitlab-ci-runner
data:
  GITLAB_CI_TOKEN: N0tGYWd4NXlmX2tzTjFzN3pGc2EK #将token进行base64编码后的字符串
```

**接下来编写用于运行Runner的控制器对象，这里使用Statefulset资源对象。首先，在开始运行的时候，尝试取消注册所有的同名Runner，然后再尝试重新注册并开始运行。另外通过使用envFrom来指定Secrets和ConfigMaps来用作环境变量，对应的资源清单文件如下：**

[root@k8s-master-node1 manifests]# vi runner-statefulset.yaml

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: gitlab-ci-runner
  namespace: kube-ops
  labels:
    app: gitlab-ci-runner
spec:
  updateStrategy:
    type: RollingUpdate
  replicas: 2
  serviceName: gitlab-ci-runner
  selector:
    matchLabels:
      app: gitlab-ci-runner
  template:
    metadata:
      labels:
        app: gitlab-ci-runner
    spec:
      volumes:
      - name: gitlab-ci-runner-scripts
        projected:
          sources:
          - configMap:
              name: gitlab-ci-runner-scripts
              items:
              - key: run.sh
                path: run.sh
                mode: 0755
      serviceAccountName: gitlab-ci
      securityContext:
        runAsNonRoot: true
        runAsUser: 999
        supplementalGroups: [999]
      containers:
      - image: yidaoyun/gitlab-runner:v1.0
        name: gitlab-ci-runner
        command:
        - /scripts/run.sh
        envFrom:
        - configMapRef:
            name: gitlab-ci-runner-cm
        - secretRef:
            name: gitlab-ci-token
        env:
        - name: RUNNER_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        ports:
        - containerPort: 9100
          name: http-metrics
          protocol: TCP
        volumeMounts:
        - name: gitlab-ci-runner-scripts
          mountPath: "/scripts"
          readOnly: true
      restartPolicy: Always
```

**上面使用了一个名为gitlab-ci的serviceAccount，新建一个rbac资源清单文件：**

[root@k8s-master-node1 manifests]# vi runner-rbac.yaml

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: gitlab-ci
  namespace: kube-ops
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: gitlab-ci
  namespace: kube-ops
rules:
  - apiGroups: [""]
    resources: ["*"]
    verbs: ["*"]
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: gitlab-ci
  namespace: kube-ops
subjects:
  - kind: ServiceAccount
    name: gitlab-ci
    namespace: kube-ops
roleRef:
  kind: Role
  name: gitlab-ci
  apiGroup: rbac.authorization.k8s.io
```

#### （3）部署GitLab CI Runner
**创建Runner资源对象：**

[root@k8s-master-node1 manifests]# kubectl apply -f runner-configmap.yaml -f runner-scripts-configmap.yaml -f runner-token-secret.yaml -f runner-statefulset.yaml -f runner-rbac.yaml

configmap/gitlab-ci-runner-cm created

configmap/gitlab-ci-runner-scripts created

secret/gitlab-ci-token created

statefulset.apps/gitlab-ci-runner created

serviceaccount/gitlab-ci created

role.rbac.authorization.k8s.io/gitlab-ci created

rolebinding.rbac.authorization.k8s.io/gitlab-ci created

**查看Pod：**

[root@k8s-master-node1 manifests]# kubectl -n kube-ops get pods

NAME                 READY   STATUS    RESTARTS   AGE

gitlab-8656b798ff-z54sm      1/1       Running     0           32m

gitlab-ci-runner-0         1/1       Running     0           32s

gitlab-ci-runner-1         1/1       Running     0           85s

**回到admin界面查看：**

![[1688705558211-9570b12b-d901-4793-98ca-c47bd8616998.png]]

可以看到Runner已经注册成功。

### 5.3 配置GitLab
#### （1）开启Container Registry
进入我们的项目，依次点击“设置”→“CI/CD”，如图所示：

![[1688705890631-6e3e70bd-2454-4e59-b02d-b0f7b2db7e32.png]]

展开“变量”栏目，配置镜像仓库相关的参数。

**key : value的形式**

REGISTRY (192.168.100.101)  #harbor仓库IP

REGISTRY_IMAGE（springcloud）

REGISTRY_USER（admin） #harbor仓库账号

REGISTRY_PASSWORD（Harbor12345）#harbor仓库密码

REGISTRY_PROJECT（library）

HOST（192.168.100.101） #mater节点IP

#### （2）添加Kubernetes集群
Kubectl在使用的时候默认会读取当前用户目录下面的~/.kube/config文件来链接集群，所以需要在Gitlab上配置集成Kubernetes集群。

在GitLab Admin界面下，点击“设置”，展开“外发请求”，勾选“允许钩子和服务访问本地网络”，如图所示：

![[1688709105268-c747ce5d-e25b-4ba8-806b-9592c51a013a.png]]

进入项目，依次点击左侧菜单栏“运维”→“Kubernetes”，点击“添加Kubernetes集群”，选择“Add existing cluster”：

![[1688709230351-49c02222-876d-4e73-a417-1f038d6b3cfa.png]]

Kubernetes 集群名称可以随便填写，如k8s-workers。

API地址是集群的apiserver的地址，可以通过命令kubectl cluster-info获取：

[root@k8s-master-node1 manifests]# kubectl cluster-info

Kubernetes control plane is running at [https://apiserver.cluster.local:6443](https://apiserver.cluster.local:6443) #https://masterIP:6443

CoreDNS is running at [https://apiserver.cluster.local:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy](https://apiserver.cluster.local:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy)

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

这个项目准备部署在一个名为gitlab的namespace下面，所以首先创建namespace：

[root@k8s-master-node1 manifests]# kubectl create ns gitlab

namespace/gitlab created

CA Certificate即CA证书，可以直接新建一个ServiceAccount，绑定cluster-admin的权限：

[root@k8s-master-node1 manifests]# vi gitlab-sa.yaml

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: gitlab
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: gitlab
  namespace: gitlab
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: gitlab
  namespace: gitlab
subjects:
  - kind: ServiceAccount
    name: gitlab
    namespace: gitlab
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
```

然后创建上面的ServiceAccount对象：

[root@k8s-master-node1 gitlab-runner]# kubectl apply -f gitlab-sa.yaml

serviceaccount/gitlab created

clusterrolebinding.rbac.authorization.k8s.io/gitlab created

通过上面创建的ServiceAccount获取CA证书和Token：

[root@k8s-master-node1 manifests]# kubectl get serviceaccount gitlab -n gitlab -o json

找到token名称

    "secrets": [

        {

            "name": "gitlab-token-8mx4g"

        }

    ]

}

然后根据上面的Secret找到CA证书和token：

[root@k8s-master-node1 manifests]# kubectl get secret gitlab-token-8mx4g  -n gitlab -o json

    "data": {

        "ca.crt": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM2VENDQWRHZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQ0FYRFRJek1EY3dOakV3TWpBME9Wb1lEekl4TWpNd05qRXlNVEF5TURRNVdqQVZNUk13RVFZRApWUVFERXdwcmRXSmxjbTVsZEdWek1JSUJJakFOQmdrcWhraUc5dzBCQVFFRkFBT0NBUThBTUlJQkNnS0NBUUVBCjAyMEFXWmRVb1BGZXRkQk9QWWJQYjlCY1Z4WW9jcnNtSXprcFRmSTN0TmNWeTNHdnhvdkVDNDVIaDY4YWx4cisKaTYwbmJZRHBmSFVUREl0a1pzTnRkRGNOK2pQc3ZyVE1oTnpyb1BDYjVpQXFZeXNrNW0rcnk2RDdlQXppRmI2TwprYVRoNHRmOG9HUEZNYVlDY1BDV2lNc1p1VFR1Q1pWWEkvcmNWTnlqbEhTT1BCbW1FUHFQZkMxLysrZDRITUNtClliclEzTnhqcy84OFBWdHpZRG1uSkFnMi9mcUVTbjNIUzVjdXNhb3k3b2d0dlRiOG9EOUZNOTZoV3haWjdCUmYKcExxSStWRHZZdDYwY0ZDM1pXd1crcFJ2VGh4SVBOVGttekUzcDdweGI5OGtqNGdRWE9XR1ExcERRNFFlTFpNSApSTjZmTWU5TG84WUQ4ZlBkdjVKczJRSURBUUFCbzBJd1FEQU9CZ05WSFE4QkFmOEVCQU1DQXFRd0R3WURWUjBUCkFRSC9CQVV3QXdFQi96QWRCZ05WSFE0RUZnUVVNRDZIcUhTS2pXNWlvMlpNZE1hV1dzSDBsZzR3RFFZSktvWkkKaHZjTkFRRUxCUUFEZ2dFQkFNV2taQ2M3VTVIVlQ4Qk01dUpoOWxNbGN3WHRrRTBFY2ZRV1BadWp3cHFiSWZKNwpRdVI2WEg0c1o4RGJVOTJNNm9qQ1dPenJvNzdPcnVmYWFKa2pDdEVBT1V0VTRkZnZwOHdhSDFhaG56RkxTSFJJCkhMVk1iMEtBRzVRaitNZ2JpemxudWZTRWk0STI5eXRQbGVMdENrR29WWDgxV25MVG5nUGsvZEdvaEU4YTZBZFQKWWxlbVNzWi9VaU0vYlMvcDlGM3Q2anduN0wrNXJFdkVkb2djUkszVTVUWDdubzgwS3hURjBQUnZkS0ptb3B3ego4Q01NY3lBS1dxRlFzekVPK0JrblJGQ1MzVFl6TzF3Y3UrUm1HL2lwbFBJME1acUZORUorY0tlcUFwTkFNd0RGCmFwMmpJUDZNWi9oY3VmTXJ0T2RaNDhzYUc5eDdGckdKOUtSeWt3Zz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=",

        "namespace": "Z2l0bGFi",

        "token": "ZXlKaGJHY2lPaUpTVXpJMU5pSXNJbXRwWkNJNkltVkVWME5MVUU5V1IzQTFkMkZuTUhJeVEySndSVGMzWVhodFpra3dZbkExZGxnM09VMU1iVjlGUldNaWZRLmV5SnBjM01pT2lKcmRXSmxjbTVsZEdWekwzTmxjblpwWTJWaFkyTnZkVzUwSWl3aWEzVmlaWEp1WlhSbGN5NXBieTl6WlhKMmFXTmxZV05qYjNWdWRDOXVZVzFsYzNCaFkyVWlPaUpuYVhSc1lXSWlMQ0pyZFdKbGNtNWxkR1Z6TG1sdkwzTmxjblpwWTJWaFkyTnZkVzUwTDNObFkzSmxkQzV1WVcxbElqb2laMmwwYkdGaUxYUnZhMlZ1TFRodGVEUm5JaXdpYTNWaVpYSnVaWFJsY3k1cGJ5OXpaWEoyYVdObFlXTmpiM1Z1ZEM5elpYSjJhV05sTFdGalkyOTFiblF1Ym1GdFpTSTZJbWRwZEd4aFlpSXNJbXQxWW1WeWJtVjBaWE11YVc4dmMyVnlkbWxqWldGalkyOTFiblF2YzJWeWRtbGpaUzFoWTJOdmRXNTBMblZwWkNJNkltTXpZVGxrTURnNUxUUmhOV1F0TkRrNE5TMDRNakJoTFdOa05EQTVNR0kyWVRKak9DSXNJbk4xWWlJNkluTjVjM1JsYlRwelpYSjJhV05sWVdOamIzVnVkRHBuYVhSc1lXSTZaMmwwYkdGaUluMC5QdGZ3ZmJDVEt0dVRPQ1RRYU1kbEhrcWxDekNnWkVWNHRqMHlMQTF3bkFIZEx1YUtkMGlxNk5Yb0FhQWROZ3VJR0Q4N2h1LXpvVkhYcTNZSUppaGJQdWZlWExtajZYdmtvYVBQOTBiWVhRT1FQUnQtMXRXdTZRaS1sYkpodkE3ZEJqeUQ5d3A4Wkwza1hfZE85OG5rNHhqTElYSTFHZ05wWUxBQ2xRc25lVW5nalNxdEo2aXVhcGtZalFwYTJ4SUdVdnYxWDltX3ViSGEwTXZoRUVUMFM0NGdpSFhEcEZlT3h6SER2YkZsNkpfZllZYm1GaHhTWW50WWNUSjdJRF9yVGZTWE5YdVZRc0wzTmcxTlZqV25rOS0xN1Z2TXRjUE1oMlIwdGc0U2hxSGE2MjVFMmhwbzVERWxPZTZ0SS1XeXdvN1JqWjA5dk5JeGpDY0h5UWV2Ync="

    },

对获取的值转换格式  echo " " | base64 -d

![[1688710191992-c1ae38d7-c0b9-4627-88ba-c190c2d3ae67.png]]

![[1688710275220-b6cd66f8-ec33-4ffe-b5af-bad39afdfa19.png]]

### 5.4 .gitlab-ci.yml文件
**（1）.gitlab-ci.yml文件简介**

GitLab CI通过YAML文件管理配置job，定义了job应该如何工作。该文件存放于仓库的根目录，默认名为.gitlab-ci.yaml。

.gitlab-ci.yml文件中指定了CI的触发条件、工作内容、工作流程，编写和理解此文件是CI实战中最重要的一步，该文件指定的任务内容总体构成了1个pipeline、1个pipeline包含不同的stage执行阶段、每个stage包含不同的具体job脚本任务。

当有新内容push到仓库，或者有代码合并后，GitLab会查找是否有.gitlab-ci.yml文件，如果文件存在，Runners将会根据该文件的内容开始build本次commit。

**（2）Pipeline**

一个.gitlab-ci.yml文件触发后会形成一个pipeline任务流，由gitlab-runner来运行处理。一次Pipeline其实相当于一次构建任务，里面可以包含很多个阶段（Stages），如安装依赖、运行测试、编译、部署测试服务器、部署生产服务器等流程。任何提交或者Merge Request的合并都可以触发Pipeline构建。

**（3）Stages**

Stages表示一个构建阶段，也就是上面提到的一个流程。我们可以在一次Pipeline中定义多个Stages，这些Stages会有以下特点：

+ 所有Stages会按照顺序运行，即当一个Stage完成后，下一个Stage才会开始
+ 只有当所有Stages完成后，该构建任务(Pipeline)才会成功
+ 如果任何一个Stage失败，那么后面的Stages不会执行，该构建任务(Pipeline)失败

**（4）Jobs**

Jobs表示构建工作，表示某个Stage里面执行的工作。我们可以在Stages里面定义多个Jobs，这些Jobs会有以下特点：

+ 相同Stage中的Jobs会并行执行
+ 相同Stage中的Jobs都执行成功时，该Stage才会成功
+ 如果任何一个Job失败，那么该Stage失败，即该构建任务(Pipeline)失败

一个Job被定义为一列参数，这些参数指定了Job的行为。下表列出了主要的Job参数：

| 参数 | 是否必须 | 描述 |
| :---: | :---: | :---: |
| script | 是 | 由Runner执行的shell脚本或命令 |
| image | 否 | 用于docker镜像 |
| services | 否 | 用于docker服务 |
| stages | 否 | 定义构建阶段 |
| types | 否 | stages 的别名(已废除) |
| before_script | 否 | 定义在每个job之前运行的命令 |
| after_script | 否 | 定义在每个job之后运行的命令 |
| variable | 否 | 定义构建变量 |
| cache | 否 | 定义一组文件列表，可在后续运行中使用 |

## 6.GO项目创建发布上线

![[1688712047517-bee55392-db66-48ed-8507-09fcb4819e4e.png]]
