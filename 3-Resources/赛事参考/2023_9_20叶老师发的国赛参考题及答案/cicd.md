# 国赛gitlab-runner(CICD-Runners.tar.gz)
该公司决定采用 Kubernetes + GitLab CI 来构建 CICD 环境，以缩短新功能开发上线周期，及时满足客户的需求，实现 DevOps 的部分流程，来减轻部署运维的负担，实现可视化容器生命周期管理、应用发布和版本迭代更新，请完成GitLab CI + Kubernetes 的 CICD 环境部署（构建持续集成所需要的所有软件包在软件包 CICD-Runner.tar.gz 中）。CICD 应用系统架构如下：

![[1695709211828-98f67fce-9d39-47c7-b44f-c45e5257d3a0.jpeg]]

## 【题目 1】安装GitLab 环境[1 分]

在Kubernetes 集群中新建命名空间gitlab-ci，将GitLab 部署到该命名空间下，Deployment和 Service 名称均为gitlab，以 NodePort 方式将 80 端口对外暴露为 30880，设置 GitLab 服务root 用户的密码为 admin@123，将项目包demo-2048.tar.gz 导入到 GitLab 中并命名为demo-2048。

完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径CICD-Runners.tar.gz）

[root@k8s-master-node1 ~]# tar -zxvf CICD-Runners.tar.gz

[root@k8s-master-node1 ~]# cd cicd-runner/

[root@k8s-master-node1 cicd-runner]# docker load -i images/images.tar

[root@k8s-master-node1 cicd-runner]# scp images/images.tar 192.168.10.80:/opt/

[root@k8s-worker-node1 opt]# docker load -i images/images.tar  #注意到worker节点也上传镜像

[root@k8s-master-node1 cicd-runner]# cat gitlab-deployment.yaml

apiVersion: apps/v1

kind: Deployment

metadata:

  name: gitlab

  namespace: gitlab-ci

  labels:

    app: gitlab

spec:

  replicas: 1

  selector:

    matchLabels:

     app: gitlab

  template:

    metadata:

      labels:

        app: gitlab

    spec:

      containers:

        - name: gitlab

          image: gitlab/gitlab-ce:latest

          imagePullPolicy: IfNotPresent

          ports:

            - name: gitlab-80

              containerPort: 80

          env:

            - name: GITLAB_ROOT_PASSWORD

              value: admin@123

---

apiVersion: v1

kind: Service

metadata:

  name: gitlab

  namespace: gitlab-ci

  labels:

     app: gitlab

spec:

  type: NodePort

  selector:

    app: gitlab

  ports:

    - port: 80

      targetPort: 80

      name: gitlab-80

      nodePort: 30880

[root@k8s-master-node1 cicd-runner]# kubectl create ns gitlab-ci

[root@k8s-master-node1 cicd-runner]# kubectl apply -f gitlab.yaml

[root@k8s-master-node1 cicd-runner]# kubectl get -f gitlab.yaml

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE

deployment.apps/gitlab   1/1     1            1           4m35s

NAME             TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE

service/gitlab   NodePort   10.96.191.24   <none>        80:30880/TCP   4m35s

[root@k8s-master-node1 cicd-runner]# kubectl get pod -n gitlab-ci

NAME                      READY   STATUS    RESTARTS   AGE

gitlab-5df4dfbddc-xxrqv   1/1     Running   0          4m52s

[root@k8s-master-node1 cicd-runner]# kubectl get svc -n gitlab-ci

NAME     TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE

gitlab   NodePort   10.96.191.24   <none>        80:30880/TCP   4m56s

等1分钟到浏览器下输入http://ip:30880访问gitlab

输入账号root，密码admin@123登录

![[1695709212338-9ff1c539-a698-48da-91f3-8c9566494a5e.png]]

![[1695709212569-af0253ff-f1c0-4105-8849-02fbbe1d8d9b.png]]![[1695709212798-d89ea33f-8315-433e-a215-030765d6fbe0.png]]![[1695709212984-39a19b0d-0258-418d-8bb8-970a4e473e9f.png]]![[1695709213222-fe3a98b3-b221-4ee4-905c-fbad79d74fa1.png]]

demo-2048.tar.gz导入成功如下：

![[1695709213468-347a60ca-e2a7-4e1e-ad33-c275fd455738.png]]

## 【题目 2】部署GitLab Runner[2 分]

将 GitLab Runner 部署到 gitlab-ci 命名空间下，Release 名称为 gitlab-runner，为 GitLab Runner 创建持久化构建缓存目录/home/gitlab-runner/ci-build-cache 以加速构建速度，并将其注册到 GitLab 中。

完成后提交 master 节点的用户名、密码和 IP 地址到答题框。（需要用到的软件包路径CICD-Runner.tar.gz）

查看GitLab Pod的IP地址：

[root@k8s-master-node1 cicd-runner]# kubectl get pod -n gitlab-ci -o wide

NAME                             READY   STATUS    RESTARTS   AGE     IP            NODE               NOMINATED NODE   READINESS GATES

gitlab-5df4dfbddc-xxrqv          1/1     Running   0          5h18m   10.244.1.15   k8s-worker-node1   <none>           <none>

查看集群的主机名：

[root@k8s-master-node1 cicd-runner]# kubectl cluster-info

Kubernetes control plane is running at [https://apiserver.cluster.local:6443](https://apiserver.cluster.local:6443)

CoreDNS is running at [https://apiserver.cluster.local:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy](https://apiserver.cluster.local:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy)

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

[root@k8s-master-node1 cicd-runner]#  kubectl edit configmap coredns -n kube-system

 kubernetes cluster.local in-addr.arpa ip6.arpa {

           pods insecure

           fallthrough in-addr.arpa ip6.arpa

           ttl 30

        }

        hosts {  #在如下位置添加红色部分

            10.244.1.15 gitlab-5df4dfbddc-xxrqv

            192.168.10.70 apiserver.cluster.local

            fallthrough

        }

        prometheus :9153

        cache 30

        loop

        reload

        loadbalance

    }

[root@k8s-master-node1 cicd-runner]# kubectl -n kube-system rollout restart deployment coredns

deployment.apps/coredns restarted

开启本地连接：

![[1695709213765-e5008527-12c3-4939-9b50-b45112199b17.png]]

![[1695709214025-4c824202-de38-4c41-b819-8badcef352da.png]]![[1695709214534-36e2aba8-b33b-4dc4-a7a9-569d8602570c.png]]

获取runnerRegistrationToken

![[1695709214917-aa0a9cea-b0c0-41fa-bd6d-506136ae6a47.png]]

[root@k8s-master-node1 cicd-runner]# tar -zxvf gitlab-runner-0.46.0.tgz

[root@k8s-master-node1 cicd-runner]# cd gitlab-runner/

[root@k8s-master-node1 gitlab-runner]# docker tag gitlab/gitlab-runner:latest registry.gitlab.com/gitlab/gitlab-runner:latest

[root@k8s-worker-node1 ~]# docker tag gitlab/gitlab-runner:latest registry.gitlab.com/gitlab/gitlab-runner:latest

[root@k8s-master-node1 gitlab-runner]# ls

CHANGELOG.md  CONTRIBUTING.md  sMakefile  README.md  values.yaml

Chart.yaml    LICENSE          NOTICE    templates

[root@k8s-master-node1 gitlab-runner]# cat values.yaml

……

image:

  registry: registry.gitlab.com

  image: gitlab/gitlab-runner  #修改为gitlab、gitlab-runner

  tag: latest  #去掉注释把正确的tag填上

……

## Timeout, in seconds, for liveness and readiness probes of a runner pod.

# probeTimeoutSeconds: 1

## How many runner pods to launch.

##

## Note: Using more than one replica is not supported with a runnerToken. Use a runnerRegistrationToken

## to create multiple runner replicas.

replicas: 2  #两个模板

……

## The GitLab Server URL (with protocol) that want to register the runner against

## ref: [https://docs.gitlab.com/runner/commands/index.html#gitlab-runner-register](https://docs.gitlab.com/runner/commands/index.html#gitlab-runner-register)

##

gitlabUrl: [http://192.168.10.70:30880/](http://192.168.10.70:30880/) #去掉注释，把gitlab URL填上

……

## The Registration Token for adding new Runners to the GitLab Server. This must

## be retrieved from your GitLab Instance.

## ref: [https://docs.gitlab.com/ce/ci/runners/index.html](https://docs.gitlab.com/ce/ci/runners/index.html)

##

runnerRegistrationToken: "QmpkjB_ZBSC4UqKpQV-x"  #把前面复制的token粘贴进来

……

 ## ref: [https://kubernetes.io/docs/concepts/configuration/secret/](https://kubernetes.io/docs/concepts/configuration/secret/)

  cache:  #去掉{}

    ## General settings

    ## DEPRECATED: See [https://docs.gitlab.com/runner/install/kubernetes.html#additional-configuration](https://docs.gitlab.com/runner/install/kubernetes.html#additional-configuration) and [https://docs.gitlab.com/runner/install/kubernetes.html#using-cache-with-configuration-template](https://docs.gitlab.com/runner/install/kubernetes.html#using-cache-with-configuration-template)

    cacheType: s3   #去掉注释

    cachePath: "/home/gitlab-runner/ci-build-cache"   #去掉注释，填入题目要求目录

    cacheShared: true   #去掉注释

……

## Configure securitycontext valid for the whole pod

## ref: [http://kubernetes.io/docs/user-guide/security-context/](http://kubernetes.io/docs/user-guide/security-context/)

##

podSecurityContext:

  runAsUser: 999  #修改为999

  # runAsGroup: 65533

  fsGroup: 65533

  # supplementalGroups: [65533]

[root@k8s-master-node1 gitlab-runner]# helm install -n gitlab-ci gitlab-runner .

NAME: gitlab-runner

LAST DEPLOYED: Sat Aug  5 16:07:12 2023

NAMESPACE: gitlab-ci

STATUS: deployed

REVISION: 1

TEST SUITE: None

NOTES:

Your GitLab Runner should now be registered against the GitLab instance reachable at: "http://192.168.10.70:30880/"

Runner namespace "gitlab-ci" was found in runners.config template.

[root@k8s-master-node1 gitlab-runner]# kubectl get pod -n gitlab-ci

NAME                             READY   STATUS    RESTARTS   AGE

gitlab-5df4dfbddc-xxrqv          1/1     Running   0          5h14m

gitlab-runner-5b6f5bb698-5qdjb   1/1     Running   0          21s

gitlab-runner-5b6f5bb698-9kgqm   1/1     Running   0          21s

回到浏览器查看

![[1695709215200-a20b9282-b740-4b27-9294-cb1b2d4d1dcf.png]]

## 【题目 3】配置GitLab[1.5 分]
将 Kubernetes 集群添加到 demo-2048 项目中，并命名为 kubernetes-agent，项目命名空间选择 gitlab-ci。

完成后提交 master 节点的用户名、密码和 IP 地址到答题框。（需要用到的软件包路径CICD-Runners.tar.gz）

[root@k8s-master-node1 cicd-runner]# kubectl exec -it -n gitlab-ci gitlab-5df4dfbddc-xxrqv -- bash

root@gitlab-5df4dfbddc-xxrqv:/# cat /etc/gitlab/gitlab.rb

##! Settings used by the GitLab application

# gitlab_rails['gitlab_kas_enabled'] = true

# gitlab_rails['gitlab_kas_external_url'] = ws://gitlab.example.com/-/kubernetes-

# gitlab_rails['gitlab_kas_internal_url'] = grpc://localhost:8153

# gitlab_rails['gitlab_kas_external_k8s_proxy_url'] = ws://gitlab.example.com/-/k

##! Enable GitLab KAS

gitlab_kas['enable'] = true    #开启kas功能

root@gitlab-5df4dfbddc-xxrqv:/# gitlab-ctl reconfigure

![[1695709215492-f0d0a942-c5be-48a8-9d63-9d7486f2f6a4.png]]

![[1695709215774-f6fa7edc-a113-4801-8a6f-20a2c2e8f21c.png]]

![[1695709216040-bf9ae54d-4bc5-4595-9264-7a7e1333e106.png]]

![[1695709216290-48072db9-2ada-438e-8c71-72227cd23351.png]]

![[1695709216645-86f5adb5-7fe2-411c-bf7f-1c4e958dd157.png]]

也可以创建空文件，不用输入内容(没有这个.gitlab目录下的这个文件的话，是等于没有代理的，必须要创建)

![[1695709216890-ac0614e8-580d-4237-a365-62f853e1e9f8.png]]

![[1695709217118-435c46ca-93ae-4397-82bb-037bb26681ce.png]]

![[1695709217363-271e0225-347e-4e06-a36f-2587666f78b1.png]]

[root@k8s-master-node1 cicd-runner]# tar -zxvf gitlab-agent-1.6.0.tgz

[root@k8s-master-node1 cicd-runner]# cd gitlab-agent/

[root@k8s-master-node1 gitlab-agent]# cat values.yaml

image:

  repository: "registry.gitlab.com/gitlab-org/cluster-integration/gitlab-agent/agentk"

  pullPolicy: IfNotPresent

  # Overrides the image tag whose default is the chart appVersion.

  tag: "v15.5.1"  #修改为正确tag……

config:

  kasAddress: 'ws://gitlab-5df4dfbddc-xxrqv/-/kubernetes-agent/'  #去掉注释粘贴前面复制的url过来（记住结尾一定要加‘/’）

  token: "2ayosLeQqaooLdhdYJz4PoA6SMuHnUVusBMPaEjhrgKBzpFyQQ"  #把前面复制的token粘贴进来

  # secretName: "token"

  # caCert: "PEM certificate file to use to verify config.kasAddress. Useful if config.kasAddress is self-signed."

[root@k8s-master-node1 gitlab-agent]# helm install kubernetes-agent -n gitlab-ci .

NAME: kubernetes-agent

LAST DEPLOYED: Sat Aug  5 20:29:28 2023

NAMESPACE: gitlab-ci

STATUS: deployed

REVISION: 1

TEST SUITE: None

[root@k8s-master-node1 gitlab-agent]# helm list -n gitlab-ci

NAME                    NAMESPACE       REVISION        UPDATED                                  STATUS          CHART                   APP VERSION

gitlab-runner           gitlab-ci       1               2023-08-05 16:07:12.06486007 +0800 CST   deployed        gitlab-runner-0.46.0    15.5.0

kubernetes-agent        gitlab-ci       1               2023-08-05 20:29:28.469549794 +0800 CST  deployed        gitlab-agent-1.6.0      v15.5.1

[root@k8s-master-node1 gitlab-agent]# kubectl get pod,secret -n gitlab-ci

NAME                                                 READY   STATUS    RESTARTS   AGE

pod/gitlab-5df4dfbddc-xxrqv                          1/1     Running   0          9h

pod/gitlab-runner-5b6f5bb698-5qdjb                   1/1     Running   0          4h22m

pod/gitlab-runner-5b6f5bb698-9kgqm                   1/1     Running   0          4h22m

pod/kubernetes-agent-gitlab-agent-555c5dbd9c-79jl2   1/1     Running   0          21s

NAME                                               TYPE                                  DATA   AGE

secret/default-token-k9tj4                         kubernetes.io/service-account-token   3      9h

secret/gitlab-runner                               Opaque                                2      4h22m

secret/kubernetes-agent-gitlab-agent-token         Opaque                                1      21s

secret/kubernetes-agent-gitlab-agent-token-lvrwl   kubernetes.io/service-account-token   3      21s

secret/sh.helm.release.v1.gitlab-runner.v1         helm.sh/release.v1                    1      4h22m

secret/sh.helm.release.v1.kubernetes-agent.v1      helm.sh/release.v1                    1      21s

![[1695709217595-b79eec34-3f05-4e56-b04a-35c6adeb8105.png]]

## 【题目 4】构建CICD[3.5 分]
编写流水线脚本.gitlab-ci.yml触发自动构建，具体要求如下：

（1）基于镜像maven:3.6-jdk-8构建项目的drone分支；

（2）构建镜像的名称：demo:latest；

（3）将镜像推送到Harbor仓库demo项目中；

（4）将demo-2048应用自动发布到Kubernetes集群gitlab-ci命名空间下。

完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径CICD-Runners.tar.gz）

curl -L http://$(hostname -i):8889

Join the numbers and get to the **2048 tile!

docker pull $(hostname -i)/demo/demo:latest && docker inspect $(hostname -i)/demo/demo:latest

TOMCAT_MAJOR=8 || TOMCAT_VERSION=8.5.64

kubectl -n gitlab-ci get all

deployment.apps/demo-2048 1/1

![[1695709217896-146a8224-07b6-42fd-b21f-6b0b3422cb25.png]]

deployment依旧采用apache或者nginx的镜像来启动用作偷鸡（记得把8080端口改回80,svc相对应的也改回来）

![[1695709218249-260e0e1e-a373-468f-ac29-32e9ed8aa2d4.png]]

# k8s部署jenkins新版-jenkins Slave
该公司决定采用GitLab +Jenkins来构建CICD环境，以缩短新功能开发上线周期，及时满足客户的需求，实现DevOps的部分流程，来减轻部署运维的负担，实现可视化容器生命周期管理、应用发布和版本迭代更新，请完成GitLab + Jenkins + Kubernetes的CICD环境部署（构建持续集成所需要的所有软件包在软件包Jenkins-Slave.tar.gz中）。CICD应用系统架构如下：

![[1695709218552-3c14ccf9-0e6c-431a-bb95-9d6aae768874.png]]

【适用平台】私有云

## 【题目1】安装Jenkins环境
使用NFS作为Kubernetes后端存储，在Kubernetes集群中部署动态存储nfs-storage。然后使用镜像jenkins/Jenkins:latest在Kubernetes集群defaul命名空间下完成Jenkins的部署，Deployment和Service名称均为jenkins，以NodePort方式将Jenkins的8080端口对外暴露为30880，使用nfs-storage实现Jenkins持久化存储，并完成离线插件的安装，设置管理员用户jenkins，密码为000000。

完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径http://<IP>/Jenkins-Slave.tar.gz）

第一种方法：上传镜像并配置动态存储（但太麻烦了，不建议）

因为k8s 1.22.0版本以上的配置不支持起动态sc，需要在配置文件加上一条参数（1.25.1就不用加了，因为锁定为ture了）

[root@k8s-master-node1 ~]# cat /etc/kubernetes/manifests/kube-apiserver.yaml

apiVersion: v1

kind: Pod

metadata:

  annotations:

    kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 192.168.10.70:6443

  creationTimestamp: null

  labels:

    component: kube-apiserver

    tier: control-plane

  name: kube-apiserver

  namespace: kube-system

spec:

  containers:

  - command:

    - kube-apiserver

    - --feature-gates=RemoveSelfLink=false #在这个位置添加这条参数

[root@k8s-master-node1 ~]# systemctl daemon-reload

[root@k8s-master-node1 ~]# kubectl get pod #如下显示是正常的，等一下就好了

The connection to the server apiserver.cluster.local:6443 was refused - did you specify the right host or port?

[root@k8s-master-node1 ~]# kubectl get pod

No resources found in default namespace.

#上传nfs镜像

[root@k8s-master-node1 ~]# docker load -i nfs-subdir-external-provisioner_v4.0.1.tar

[root@k8s-master-node1 ~]# tar -zxvf Jenkins-Slave.tar.gz

[root@k8s-master-node1 ~]# cd Jenkins-Slave/

[root@k8s-master-node1 Jenkins-Slave]# docker load -i images/images.tar

[root@k8s-master-node1 Jenkins-Slave]# mkdir -p /data/k8s

[root@k8s-master-node1 Jenkins-Slave]# chmod 777 /data/k8s/ #一定要给权限

[root@k8s-master-node1 Jenkins-Slave]# cat /etc/exports

/data/k8s 192.168.10.0/24(rw)

[root@k8s-master-node1 Jenkins-Slave]# systemctl restart rpcbind

[root@k8s-master-node1 Jenkins-Slave]# systemctl restart nfs

[root@k8s-master-node1 Jenkins-Slave]# showmount -e localhost

Export list for localhost:

/data/k8s 192.168.10.0/24

[root@k8s-master-node1 Jenkins-Slave]# kubectl create sa sa-nfs  #创建sa

serviceaccount/sa-nfs created

 [root@k8s-master-node1 Jenkins-Slave]# kubectl create clusterrolebinding --clusterrole cluster-admin --serviceaccount default:sa-nfs clusterrolebinding-nfs

clusterrolebinding.rbac.authorization.k8s.io/clusterrolebinding-nfs created

[root@k8s-master-node1 Jenkins-Slave]# cat sc-deploy.yaml

apiVersion: apps/v1

kind: Deployment

metadata:

  name: nfs-deploy

  labels:

    app: nfs-deploy

  namespace: default  #与RBAC中的namespace保持一致

spec:

  replicas: 1

  selector:

    matchLabels:

      app: nfs-deploy

  #strategy:

  #  type: Recreate

  template:

    metadata:

      labels:

        app: nfs-deploy

    spec:

      serviceAccountName: sa-nfs

      containers:

        - name: nfs

          image: easzlab/nfs-subdir-external-provisioner:v4.0.1

          volumeMounts:

            - name: nfs

              mountPath: /data/k8s

          env:

            - name: PROVISIONER_NAME

              value: nfs  #provisioner名称,请确保该名称与 nfs-StorageClass.yaml文件中的provisioner名称保持一致

            - name: NFS_SERVER

              value: 192.168.10.70    #NFS Server IP地址

            - name: NFS_PATH

              value: /data/k8s    #NFS挂载卷

      volumes:

        - name: nfs

          nfs:

            server: 192.168.10.70  #NFS Server IP地址

            path: /data/k8s     #NFS 挂载卷

[root@k8s-master-node1 Jenkins-Slave]# kubectl get pod

NAME                         READY   STATUS    RESTARTS   AGE

nfs-deploy-69cc6f447-f2spl   1/1     Running   0          55s

[root@k8s-master-node1 Jenkins-Slave]# cat sc.yaml

apiVersion: storage.k8s.io/v1

kind: StorageClass

metadata:

  name: pvc-storage  #必须要这个名字，评分点

provisioner: nfs

[root@k8s-master-node1 Jenkins-Slave]# kubectl apply -f sc.yaml

storageclass.storage.k8s.io/pvc-storage created

[root@k8s-master-node1 Jenkins-Slave]# kubectl get sc

NAME     PROVISIONER   RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE

pvc-storage   nfs           Delete          Immediate           false                  5s

[root@k8s-master-node1 Jenkins-Slave]# cat pvc.yaml

apiVersion: v1

kind: PersistentVolumeClaim

metadata:

  name: pvc

spec:

  accessModes:

  - ReadWriteMany

  resources:

    requests:

      storage: 5Gi

  storageClassName: pvc-storage #这里绑定sc的时候就写sc的名字，绑定pv的时候就写pv定义的storageClassName

[root@k8s-master-node1 Jenkins-Slave]# kubectl get pvc

NAME   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE

pvc    Bound    pvc-3ef55ed9-8db2-48cc-b3ac-b811e54b4dae   5Gi        RWX            pvc-storage           36s

第二种方法：直接起个静态pv绑定到pvc就好了（沿用前面配置好的nfs）

[root@k8s-master-node1 Jenkins-Slave]# cat pv_pvc.yaml

apiVersion: v1

kind: PersistentVolume

metadata:

  name: pv

spec:

  capacity:

    storage: 5Gi

  accessModes:

  - ReadWriteMany

  storageClassName: nfs-storage

  nfs:

    path: /data/k8s

    server: 192.168.10.70

---

apiVersion: v1

kind: PersistentVolumeClaim

metadata:

  name: pvc

spec:

  accessModes:

  - ReadWriteMany

  resources:

    requests:

      storage: 5Gi

  storageClassName: nfs-storage

[root@k8s-master-node1 Jenkins-Slave]# kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM         STORAGECLASS   REASON   AGE

pv                                         5Gi        RWX            Retain           Bound      default/pvc   nfs-storage             15s

[root@k8s-master-node1 Jenkins-Slave]# kubectl get pvc

NAME   STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE

pvc    Bound    pv       5Gi        RWX            nfs-storage    16s

部署jenkins

[root@k8s-master-node1 Jenkins-Slave]# cat jenkins.yaml

apiVersion: v1

kind: Service

metadata:

  name: jenkins

spec:

  type: NodePort

  ports:

  - name: http

    port: 8080

    targetPort: 8080

    nodePort: 30880

  selector:

    app: jenkins

---

apiVersion: apps/v1

kind: Deployment

metadata:

  name: jenkins

  labels:

    app: jenkins

spec:

  selector:

    matchLabels:

      app: jenkins

  template:

    metadata:

      labels:

        app: jenkins

    spec:

      nodeName: k8s-master-node1

      containers:

      - name: jenkins

        image: jenkins/jenkins:latest

        imagePullPolicy: IfNotPresent

        securityContext:

          runAsUser: 0

          privileged: true

        ports:

        - name: http

          containerPort: 8080

        volumeMounts:

        - mountPath: /var/jenkins_home

          name: jenkinshome

        - mountPath: /usr/bin/docker

          name: docker

        - mountPath: /var/run/docker.sock

          name: dockersock

        - mountPath: /usr/bin/kubectl

          name: kubectl

        - mountPath: /root/.kube

          name: kubeconfig

      volumes:

      - name: jenkinshome

        persistentVolumeClaim:

          claimName: pvc

      - name: docker

        hostPath:

          path: /usr/bin/docker

      - name: dockersock

        hostPath:

          path: /var/run/docker.sock

      - name: kubectl

        hostPath:

          path: /usr/bin/kubectl

      - name: kubeconfig

        hostPath:

          path: /root/.kube

[root@k8s-master-node1 Jenkins-Slave]# kubectl apply -f jenkins.yaml

[root@k8s-master-node1 Jenkins-Slave]# kubectl get pod

NAME                       READY   STATUS    RESTARTS   AGE

jenkins-5668b944dc-p49hl   1/1     Running   0          107s

通过 http://master_IP:30880 访问Jenkins，如图所示：

![[1695709218969-7cb067d0-987e-4b77-ad22-ecc6f0b066a5.png]]

复制上面的路径下来获取jenkins密码

[root@k8s-master-node1 Jenkins-Slave]# kubectl exec deploy/jenkins -- cat /var/jenkins_home/secrets/initialAdminPassword

7a96e0fffb7f47d6a21c833476fb3b11

输入密码后点击“继续”之后如图所示（不要着急动，先往下离线安装插件）：

![[1695709219529-b1c47743-6fdb-4bc4-a85b-2658257fd756.png]]

将离线插件包拷贝到Jenkins：

[root@k8s-master-node1 Jenkins-Slave]# kubectl cp plugins/ jenkins-8645dd45b4-tnncq:/var/jenkins_home

重启Jenkins：

[root@k8s-master-node1 Jenkins-Slave]# kubectl rollout restart deployment jenkins

刷新 Jenkins 界面，选择“跳过插件安装”

安装完成后进入用户创建页面，创建一个用户 jenkins，密码 000000，如图所示：

![[1695709219911-9cae36b0-ad58-460b-86bb-4cd073b7b646.png]]

点击“保存并完成”，如图所示：

![[1695709220180-232f9f34-1fe3-4dd5-b970-370e77e1112c.png]]

点击“保存并完成”，如图所示：

![[1695709220528-1488d407-c3c6-46bf-af71-894affbe0187.png]]

点击“开始使用Jenkins”并使用新创建的用户登录 Jenkins，如图所示：

![[1695709220794-256b7b85-43db-4cab-aeec-cd9c0a46680c.png]]

（3）开启Jenkins 匿名访问

登录 Jenkins 首页，点击“系统管理”→“全局安全配置”，勾选“任何用户可以做任

何事(没有任何限制)”，如图所示。

![[1695709221151-f1dbca2f-a404-4cc7-bc50-87a592dc1ef2.png]]

kubectl exec deploy/jenkins -- ls /var/jenkins_home/plugins

kubernetes

curl -u jenkins:000000 [http://$(kubectl get svc jenkins -o jsonpath={.spec.clusterIP}):8080](http://$(kubectl%20get%20svc%20jenkins%20-o%20jsonpath=%7B.spec.clusterIP%7D):8080)

jenkins-2.346.3

kubectl get pvc

Bound || nfs-storage

## 【题目2】配置Jenkins Slave
在Jenkins中添加Kubernetes云，命名空间选择default，容器镜像使用jenkins/jenkins:jnlp，所有标签均为jnlp。

完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径http://<IP>/Jenkins-Slave.tar.gz）

添加kubernetes云

![[1695709221584-23060a71-1016-422e-a179-407437146aef.png]]![[1695709221875-b62b2810-f5de-417a-a28f-8cf214aa2f9b.png]]![[1695709222324-a1c42976-7cae-4155-9bdf-5fd4a6907054.png]]

![[1695709222530-1ccc433e-ac39-43c9-8f81-ab48b09b7b85.png]]

查询镜像：

[root@k8s-master-node1 Jenkins-Slave]# docker images | grep jenkins

jenkins/jenkins                                     latest            b359fee45468   9 months ago    524MB

jenkins/jenkins                                     jnlp              1040138b5448   3 years ago     1.01GB

![[1695709222779-501df54f-48fc-401c-8a51-ac7a0ede72aa.png]]

![[1695709223013-023c82a6-94bc-4a25-941f-60037bfde5ba.png]]

![[1695709223331-166c53e6-8c95-4415-9044-efa527be962f.png]]

最后点击保存即可

评分点

kubectl exec deploy/jenkins -- cat /var/jenkins_home/config.xml

jenkins/jenkins:jnlp || [http://jenkins.default.svc.cluster.local:8080](http://jenkins.default.svc.cluster.local:8080)

## 【题目3】安装GitLab环境
使用镜像gitlab/gitlab-ce:latest在Kubernetes集群default命名空间下完成GitLab的部署，Deployment和Service名称均为gitlab，以NodePort方式将GitLab的80端口对外暴露为30888。部署完成后新建公开项目cloud-manager，并将cloud-manager文件夹中的代码上传到该项目。

完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径http://<IP>/Jenkins-Slave.tar.gz）

[root@k8s-master-node1 Jenkins-Slave]# kubectl apply -f gitlab.yaml

service/gitlab created

deployment.apps/gitlab created

[root@k8s-master-node1 Jenkins-Slave]# kubectl get pod

NAME                       READY   STATUS    RESTARTS      AGE

gitlab-7f66cc64cd-x9hz2    1/1     Running   0             2s

jenkins-5668b944dc-p49hl   1/1     Running   2 (34m ago)   64m

[root@k8s-master-node1 Jenkins-Slave]# cat gitlab.yaml

apiVersion: v1

kind: Service

metadata:

  name: gitlab

spec:

  type: NodePort

  ports:

  - port: 80

    nodePort: 30888

    targetPort: 80

    name: gitlab-80

  selector:

    app: gitlab

---

apiVersion: apps/v1

kind: Deployment

metadata:

  name: gitlab

spec:

  selector:

    matchLabels:

      app: gitlab

  template:

    metadata:

      labels:

        app: gitlab

    spec:

      nodeName: k8s-master-node1

      containers:

      - image: gitlab/gitlab-ce:latest

        name: gitlab

        imagePullPolicy: IfNotPresent

        env:

        - name: GITLAB_ROOT_PASSWORD

          value: admin@123

        ports:

        - containerPort: 80

          name: gitlab-80

Gitlab 启动较慢，可以通过 docker logs 查看启动状态。启动完成后，在 web 端访问 Gitlab

（http://master_IP:30888），如图所示：

![[1695709223552-2eef23c0-59ca-426d-9f53-7eda7dea8e1a.png]]

登录Gitlab，如图所示：

![[1695709223819-0b3d03ad-64f2-4421-9c03-486edc02d643.png]]

记得到下面这个位置设置一下:

![[1695709224192-36123def-c2a1-46e8-9753-51510d1d4894.png]]

（2）.创建项目

点击“New project”，如图所示：

![[1695709224525-00d5518f-4489-4a3c-83de-bd09b79a3875.png]]

点击“Create blank project”创建项目 cloud-manager，可见等级选择“Public”，如图所示：

![[1695709224910-21792d5c-3003-4190-9cf2-c1c11beb2d32.png]]

push 源代码到 gitlab 的 cloud-manager 项目：

[root@k8s-master-node1 Jenkins-Slave]# cd cloud-manager/

[root@k8s-master-node1 cloud-manager]# git config --global user.name "Administrator"

[root@k8s-master-node1 cloud-manager]# git config --global user.email "admin@example.com"

[root@k8s-master-node1 cloud-manager]# git remote

origin

[root@k8s-master-node1 cloud-manager]# git remote remove origin

[root@k8s-master-node1 cloud-manager]# git init

重新初始化现存的 Git 版本库于 /root/Jenkins-Slave/cloud-manager/.git/

[root@k8s-master-node1 cloud-manager]# git remote add origin [http://192.168.10.70:30888/root/cloud-manager.git](http://192.168.10.70:30888/root/cloud-manager.git)

[root@k8s-master-node1 cloud-manager]# git add .

warning: 您在运行 'git add' 时没有指定 '-A (--all)' 或 '--ignore-removal'，

针对其中本地移除路径的行为将在 Git 2.0 版本库发生变化。

像本地工作区移除的路径 'cloud_manage_private.sql'

在此版本的 Git 中被忽略。

* 'git add --ignore-removal <pathspec>'，是当前版本的默认操作，

  忽略您本地工作区中移除的文件。

* 'git add --all <pathspec>' 将让您同时对删除操作进行记录。

运行 'git status' 来检查您本地工作区中移除的路径。

[root@k8s-master-node1 cloud-manager]# git commit -m "Initial commit"

# 位于分支 master

# 尚未暂存以备提交的变更：

#   （使用 "git add/rm <file>..." 更新要提交的内容）

#   （使用 "git checkout -- <file>..." 丢弃工作区的改动）

#

#       删除：      cloud_manage_private.sql

#       删除：      cloud_manage_public.sql

#

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）

[root@k8s-master-node1 cloud-manager]# git push -u origin master

Username for 'http://192.168.10.70:30888': root

Password for 'http://root@192.168.10.70:30888':

Counting objects: 242, done.

Delta compression using up to 4 threads.

Compressing objects: 100% (120/120), done.

Writing objects: 100% (242/242), 49.43 KiB | 0 bytes/s, done.

Total 242 (delta 66), reused 242 (delta 66)

remote: Resolving deltas: 100% (66/66), done.

To [http://192.168.10.70:30888/root/cloud-manager.git](http://192.168.10.70:30888/root/cloud-manager.git)

 * [new branch]      master -> master

分支 master 设置为跟踪来自 origin 的远程分支 master。

回到浏览器界面刷新显示：

![[1695709225124-a009325b-41a0-4533-8cd3-8cf71499b9e6.png]]

加入dns解析

[root@k8s-master-node1 cloud-manager]# kubectl get pod -o wide

NAME                       READY   STATUS    RESTARTS      AGE   IP            NODE               NOMINATED NODE   READINESS GATES

gitlab-7f66cc64cd-x9hz2    1/1     Running   0             17m   10.244.0.16   k8s-master-node1   <none>           <none>

jenkins-5668b944dc-p49hl   1/1     Running   2 (51m ago)   82m   10.244.0.15   k8s-master-node1   <none>           <none>

[root@k8s-master-node1 cloud-manager]# kubectl edit configmaps -n kube-system coredns

 ready

        kubernetes cluster.local in-addr.arpa ip6.arpa {

           pods insecure

           fallthrough in-addr.arpa ip6.arpa

           ttl 30

        }

        hosts {

            10.244.0.16 gitlab-7f66cc64cd-x9hz2

            10.244.0.15 jenkins-5668b944dc-p49hl

            fallthrough

        }

        prometheus :9153

评分点

kubectl exec -i deploy/gitlab -- gitlab --version

Gitlab Ruby Gem 4.16.1

curl -L [http://$(kubectl get svc gitlab -o jsonpath={.spec.clusterIP})](http://$(kubectl%20get%20svc%20gitlab%20-o%20jsonpath=%7B.spec.clusterIP%7D))

navbar-gitlab

## 【题目4】安装RabbitMQ和Nacos环境
使用提供的YAML文件在Kubernetes集群default命名空间下完成RabbitMQ和Nacos环境的部署，将cloud_manage_public.sql导入数据库，并根据实际环境修改Nacos的配置管理。

完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径http://<IP>/Jenkins-Slave.tar.gz）

[root@k8s-master-node1 Jenkins-Slave]# cat rabbitmq.yaml

apiVersion: apps/v1

kind: Deployment

metadata:

  name: rabbitmq

  labels:

    app: rabbitmq

spec:

  selector:

    matchLabels:

      app: rabbitmq

  template:

    metadata:

      labels:

        app: rabbitmq

    spec:

      nodeName: k8s-master-node1

      containers:

      - name: rabbitmq

        image: rabbitmq:3.7-rc-management

        imagePullPolicy: IfNotPresent

        ports:

        - name: http

          containerPort: 15672

        env:

        - name: RABBITMQ_DEFAULT_USER

          value: 'admin'

        - name: RABBITMQ_DEFAULT_PASS

          value: '123456'

        - name: RABBITMQ_DEFAULT_COOKIE

          value: '123456'

[root@k8s-master-node1 Jenkins-Slave]# kubectl apply -f rabbitmq.yaml

[root@k8s-master-node1 Jenkins-Slave]# kubectl get pod

NAME                        READY   STATUS    RESTARTS        AGE

gitlab-7f66cc64cd-x9hz2     1/1     Running   0               4h39m

jenkins-5668b944dc-p49hl    1/1     Running   2 (5h13m ago)   5h43m

rabbitmq-6ff4b46fc9-8s86b   1/1     Running   0               113s

[root@k8s-master-node1 Jenkins-Slave]# cat mysql.yaml

apiVersion: v1

kind: ReplicationController

metadata:

  name: mysql

spec:

  selector:

    app: mysql

  template:

    metadata:

      labels:

        app: mysql

    spec:

      nodeName: k8s-master-node1

      containers:

      - name: mysql

        image: nacos/nacos-mysql:5.7

        imagePullPolicy: IfNotPresent

        ports:

        - name: http

          containerPort: 3306

        env:

        - name: MYSQL_ROOT_PASSWORD

          value: 'root'

        - name: MYSQL_DATABASE

          value: nacos

        - name: MYSQL_USER

          value: nacos

        - name: MYSQL_PASSWORD

          value: 'root'

[root@k8s-master-node1 Jenkins-Slave]# kubectl get pod

NAME                        READY   STATUS    RESTARTS      AGE

gitlab-7f66cc64cd-x9hz2     1/1     Running   0             16h

jenkins-5668b944dc-p49hl    1/1     Running   2 (17h ago)   17h

mysql-x7k5p                 1/1     Running   0             3m52s

rabbitmq-6ff4b46fc9-8s86b   1/1     Running   0             12h

[root@k8s-master-node1 cloud-manager]# kubectl cp cloud-manager/cloud_manage_public.sql mysql-x7k5p:/opt/

[root@k8s-master-node1 cloud-manager]# kubectl exec -it mysql-x7k5p -- bash

root@mysql-x7k5p:/# mysql -uroot -proot -e "create database cloud_manage_public; use cloud_manage_public; source /opt/cloud_manage_public.sql;"

mysql: [Warning] Using a password on the command line interface can be insecure.

root@mysql-x7k5p:/# mysql -uroot -proot

mysql> grant all on *.* to nacos@'%' identified by 'nacos';

Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> exit

Bye

root@mysql-x7k5p:/# exit

Exit

[root@k8s-master-node1 Jenkins-Slave]# cat nacos.yaml

apiVersion: apps/v1

kind: StatefulSet

metadata:

  name: nacos

  labels:

    app: nacos

spec:

  serviceName: nacos

  selector:

    matchLabels:

      app: nacos

  template:

    metadata:

      labels:

        app: nacos

    spec:

      nodeName: k8s-master-node1

      containers:

      - name: nacos

        image: nacos/nacos-server:latest

        imagePullPolicy: IfNotPresent

        ports:

        - name: http

          containerPort: 8080

        env:

        - name: MYSQL_SERVICE_HOST  #上面mysql的pod的ip

          value: '10.244.0.19'

        - name: MYSQL_SERVICE_DB_NAME

          value: 'nacos'

        - name: MYSQL_SERVICE_PORT

          value: '3306'

        - name: MYSQL_SERVICE_USER

          value: 'nacos'

        - name: MYSQL_SERVICE_PASSWORD

          value: 'root'

[root@k8s-master-node1 Jenkins-Slave]# kubectl apply -f nacos.yaml

# 5. 查看日志没有报错即可

[root@master Jenkins-Slave]# kubectl logs nacos-7dcd979559-5dvjm

,--.

,--.'|

,--,: : | Nacos 2.0.3

,`--.'`| ' : ,---. Running in cluster mode, All

function modules

| : : | | ' ,'\ .--.--. Port: 8848

: | \ | : ,--.--. ,---. / / | / / ' Pid: 1

| : ' '; | / \ / \. ; ,. :| : /`./ Console:

[http://10.244.219.67:8848/nacos/index.html](http://10.244.219.67:8848/nacos/index.html)' ' ;. ;.--. .-. | / / '' | |: :| : ;_

| | | \ | \__\/: . .. ' / ' | .; : \ \ `. [https://nacos.io](https://nacos.io)

' : | ; .' ," .--.; |' ; :__| : | `----. \

| | '`--' / / ,. |' | '.'|\ \ / / /`--' /

' : | ; : .' \ : : `----' '--'. /

; |.' | , .-./\ \ / `--'---'

'---' `--`---' `----'

# 5. 测试访问

[root@master Jenkins-Slave]# curl [http://10.244.219.67:8848/nacos/index.html](http://10.244.219.67:8848/nacos/index.html)

<!--

~ Copyright 1999-2018 Alibaba Group Holding Ltd.

~

~ Licensed under the Apache License, Version 2.0 (the "License");

~ you may not use this file except in compliance with the License.

~ You may obtain a copy of the License at

~

~ [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

~

~ Unless required by applicable law or agreed to in writing, software

~ distributed under the License is distributed on an "AS IS" BASIS,

~ WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.

~ See the License for the specific language governing permissions and

~ limitations under the License.

-->

<!DOCTYPE html>

<html lang="en">

<head>

<meta charset="UTF-8">

<meta name="viewport" content="width=device-width,initial-scale=1">

<meta http-equiv="X-UA-Compatible" content="ie=edge">

<title>Nacos</title>

<link rel="shortcut icon" href="console-ui/public/img/favicon.ico"

type="image/x-icon">

<link rel="stylesheet" type="text/css" href="console

ui/public/css/bootstrap.css">

<link rel="stylesheet" type="text/css" href="console

ui/public/css/console1412.css">

<!-- 第三方css开始 -->

<link rel="stylesheet" type="text/css" href="console

ui/public/css/codemirror.css">

<link rel="stylesheet" type="text/css" href="console-ui/public/css/merge.css">

<link rel="stylesheet" type="text/css" href="console-ui/public/css/icon.css">

<link rel="stylesheet" type="text/css" href="console-ui/public/css/font

awesome.css">

<!-- 第三方css结束 -->

<link href="css/main.css" rel="stylesheet">

评分点

kubectl exec replicationcontroller/mysql -- mysql -uroot -proot -e "use cloud_manage_public;show tables;"

huawei_cloud_credential

ip=$(hostname -i) && kubectl exec statefulset.apps/nacos -- grep -r $ip /home/nacos/data/config-data/DEFAULT_GROUP/cloud-manage-public.yaml

jdbc:mysql || host

## 【题目5】构建CI/CD
在Jenkins中新建流水线任务cloud-manage-public，流水线选择“Pipeline script from SCM”。在cloud-manage项目中新建Jenkinsfile脚本文件，编写声明式Pipeline，触发项目构建，要求完成项目代码的编译，然后构建Docker镜像并推送到Harbor仓库的library项目，并基于新构建的镜像将服务自动发布到Kubernetes集群cloud-manage命名空间下。

完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径http://<IP>/BlueOcean.tar.gz）

kubectl exec -i deploy/jenkins -- bash -c 'grep -r "Finished: SUCCESS" /var/jenkins_home/jobs/cloud-manage-public/builds'

Finished: SUCCESS

kubectl exec -i deploy/jenkins -- bash -c 'grep -r "mvn -B -DskipTests clean package" /var/jenkins_home/jobs/cloud-manage-public/builds'

mvn -B -DskipTests clean package

curl [http://$(kubectl -n cloud-manage get svc nginx-service -o jsonpath={.spec.clusterIP}):8095](http://$(kubectl%20-n%20cloud-manage%20get%20svc%20nginx-service%20-o%20jsonpath=%7B.spec.clusterIP%7D):8095)

kubernetes || openstack

kubectl exec -i deploy/jenkins -- bash -c 'grep -r "slave" /var/jenkins_home/jobs/cloud-manage-public/builds'

jenkins: "slave"

kubectl exec -i deploy/jenkins -- bash -c 'grep -r "jenkins/label" /var/jenkins_home/jobs/cloud-manage-public/builds'

jenkins/label: "jnlp"
