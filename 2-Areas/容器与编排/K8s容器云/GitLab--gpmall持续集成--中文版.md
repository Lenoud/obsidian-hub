# 基于Kubernetes + GitLab构建CICD

基于Kubernetes + GitLab构建CICD相关技术笔记。
## 模块简介
本主题介绍如何在Kubernetes集群中安装和注册GitLab运行器，并添加Kubernetes执行器来构建应用程序。本主；题还提供了逐步实现持续集成(CI)/持续交付(CD)管道的示例，该管道包括多个阶段，如源代码编译、映像构建和推送以及应用程序部署。

## 知识点
1. 在kubernetes集群中安装GitLab Runner；
2. 使用GitLab CI运行GitLab运行器；

（3）设置一个 Kubernetes 执行器。

## 环境准备
### 一、节点规划
登录一台已安装了CentOS7.5操作系统的虚拟机，节点规划见表1-1。

表1-1 节点规划

| **IP** | **主机名** | **节点** |
| --- | --- | --- |
| 10.24.194.193 | master | master节点 |

**具体内容**

### 一、概述
#### 1.1 CI/CD定义
CI 和 CD 代表持续集成和持续交付/持续部署。简而言之，CI 是一种现代软件开发实践，其中频繁且可靠地进行增量代码更改。CI 触发的自动化构建和测试步骤确保合并到存储库中的代码更改是可靠的。然后，代码将作为 CD 流程的一部分快速、无缝地交付。在软件世界中，CI/CD 管道是指使开发人员桌面上的增量代码更改能够快速可靠地交付到生产环境的自动化。

#### 1.2 CI和CD有什么区别
持续集成 (CI) 是一种涉及开发人员对其代码进行微小更改和检查的实践。由于需求的规模和所涉及的步骤数量，这个过程是自动化的，以确保团队能够以可靠和可重复的方式构建、测试和打包他们的应用程序。[CI](https://www.synopsys.com/glossary/what-is-continuous-integration.html)有助于简化代码更改，从而增加开发人员进行更改并为改进软件做出贡献的时间。

[持续交付](https://www.synopsys.com/glossary/what-is-continuous-delivery.html" \t "https://www.synopsys.com/glossary/_self)(CD) 是将完整代码自动交付到测试和开发等环境。CD 为将代码交付到这些环境提供了一种自动化且一致的方式。

[持续部署](https://www.synopsys.com/glossary/what-is-continuous-development.html" \t "https://www.synopsys.com/glossary/_self)是持续交付的下一步。通过自动化测试的每个更改都会自动投入生产，从而导致许多生产部署。

持续部署应该是大多数不受监管或其他要求约束的公司的目标。

简而言之，CI 是开发人员在编写代码时执行的一组实践，而 CD 是代码完成后执行的一组实践。

#### 1.3 CI/CD与DevOps有什么关系
DevOps 是一组实践和工具，旨在提高组织以比传统软件开发流程更快的速度交付应用程序和服务的能力。DevOps 速度的提高有助于组织更成功地为其客户提供服务，并在市场上更具竞争力。在 DevOps 环境中，成功的组织将安全性“融入”到开发生命周期的所有阶段，这种做法称为[DevSecOps](https://www.synopsys.com/software-integrity/solutions/devsecops.html" \t "https://www.synopsys.com/glossary/_self)。

DevSecOps 的关键实践是将安全性集成到所有 DevOps 工作流中。通过在整个软件开发生命周期 ( [SDLC](https://www.synopsys.com/glossary/what-is-sdlc.html" \t "https://www.synopsys.com/glossary/_self) ) 中尽早且始终如一地开展安全活动，组织可以确保他们尽早发现漏洞，并且能够更好地就风险和缓解做出明智的决策。在更传统的安全实践中，直到生产阶段才解决安全问题，这不再与更快、更敏捷的 DevOps 方法兼容。如今，安全工具必须无缝融入开发人员工作流程和 CI/CD 管道，以便跟上 DevOps 的步伐，而不是降低开发速度。 

CI/CD 管道是更广泛的 DevOps/DevSecOps 框架的一部分。为了成功实施和运行 CI/CD 管道，组织需要工具来防止减缓集成和交付的摩擦点。团队需要一个集成的技术工具链来促进协作和畅通无阻的开发工作。

#### 1.4 [什么是GitLab?](https://www.simplilearn.com/tutorials/git-tutorial/what-is-gitlab" \l "what_is_gitlab" \o "What is GitLab?)
GitLab 是一个基于 Web 的[Git 存储库](https://www.simplilearn.com/tutorials/git-tutorial/git-tutorial-for-beginner" \o "Git 存储库" \t "https://www.simplilearn.com/tutorials/git-tutorial/_blank)，提供免费的开放和私有存储库、问题跟踪功能和 wiki。它是一个完整的 DevOps 平台，使专业人员能够执行项目中的所有任务——从项目规划和源代码管理到监控和安全。此外，它允许团队协作并构建更好的软件。 

GitLab 帮助团队缩短产品生命周期并提高生产力，从而为客户创造价值。该应用程序不需要用户管理每个工具的授权。如果权限设置一次，那么组织中的每个人都可以访问每个组件。

#### 1.5 GitLab Runner
GitLab Runner 是一个与 GitLab CI/CD 一起使用以在管道中运行作业的应用程序。

您可以选择在您拥有或管理的基础设施上[安装](https://docs.gitlab.com/runner/install/index.html)GitLab Runner 应用程序。如果这样做，出于安全和性能原因，您应该将 GitLab Runner 安装在与托管 GitLab 实例的机器不同的机器上。当您使用不同的机器时，您可以在每台机器上拥有不同的操作系统和工具，例如 Kubernetes 或 Docker。

GitLab Runner 是开源的，用[Go](https://go.dev/)编写。它可以作为单个二进制文件运行；不需要特定语言的要求。

您可以在几个不同的受支持操作系统上安装 GitLab Runner。其他操作系统也可以工作，只要您可以在它们上编译 Go 二进制文件。

GitLab Runner 也可以在 Docker 容器内运行或部署到 Kubernetes 集群中。

#### 1.6 GitLab CI/CD 工作流程
GitLab CI/CD 适合通用的开发工作流程。

您可以从讨论问题中的代码实现开始，并在本地处理您提议的更改。然后，您可以将提交推送到托管在 GitLab 中的远程存储库中的功能分支。推送会触发项目的 CI/CD 管道。然后，GitLab CI/CD：

+ 运行自动化脚本（顺序或并行）以:
+ 构建和测试您的应用程序。
+ 在 Review App 中预览更改，就像您在localhost。

实施后按预期工作:

+ 让您的代码得到审查和批准。
+ 将功能分支合并到默认分支中。
+ GitLab CI/CD 自动将您的更改部署到生产环境。

如果出现问题，您可以回滚更改。

![[1685589980867-a3590ee3-2c8a-45ad-b446-a9aded0f1909.png]]

此工作流程显示了 GitLab 流程中的主要步骤。您不需要任何外部工具来交付您的软件，并且您可以在 GitLab UI 中可视化所有步骤。

### 二、实战案例——GitLab 持续集成
#### 2.1 上传镜像
拉取资源包并解压:

```bash
curl -O http://mirrors.douxuedu.com/competition/gitlab-gpamll-cicd.tar.gz

tar -zxvf gitlab-gpamll-cicd.tar.gz

docker load -i gitlab-gpamll-cicd/images.tar
```

#### 2.2 安装 GitLab
编写文件gitlab-deploy.yaml：

```yaml
[root@master ~]# vi gitlab-deploy.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: demo
---
apiVersion: v1
kind: Service
metadata:
  name: gitlab
  namespace: demo
spec:
  type: NodePort
  ports:
  - port: 443
    targetPort: 443
    name: gitlab443
  - port: 80
    nodePort: 30088
    targetPort: 80
    name: gitlab80
  - port: 22
    targetPort: 22
    name: gitlab22
  selector:
    app: gitlab
---
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: gitlab
  namespace: demo
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
        ports:
        - containerPort: 443
          name: gitlab443
        - containerPort: 80
          name: gitlab80
        - containerPort: 22
          name: gitlab22
        env:
        - name: GITLAB_ROOT_PASSWORD
          value: Abc@1234
      imagePullSecrets:
      - name: devops-repo
```

通过yaml文件安装 GitLab

```bash
[root@master ~]# kubectl apply -f gitlab-deploy.yaml
```

使用浏览器访问 masterIP:30088 以访问GitLab（用户名：root；密码Abc@1234）。

导入项目步骤

```bash
[root@master ~]# cd gitlab-gpamll-cicd/gpmall
[root@master gpmall]# git remote remove origin
[root@master gpmall]# git remote add origin http://10.24.194.193:30088/root/gpmall.git  #masterIP
[root@master gpmall]# git add .
[root@master gpmall]# git config --global user.email "you@example.com"
[root@master gpmall]# git config --global user.name "root"
[root@master gpmall]# git commit -m "Initial commit"
[root@master gpmall]# git push -u origin drone
Username for 'http://10.24.194.193:30088': root
Password for 'http://root@10.24.194.193:30088': 		#密码为Abc@1234
```

设置项目为public：

1. 在 GitLab 控制台的顶部导航栏中, 选择Projects > Your projects。
2. 在您的项目选项卡上，选择要使用的项目。
3. 在左侧导航栏中，选择Settings 。
4. 单击Visibility, project features, permissions右侧的展开。
5. Project visibility 选择public并保存。

#### 2.3 安装 GitLab Runner
安装 helm

```bash
[root@master gpmall]# mv /root/gitlab-cicd/helm  /usr/local/bin/helm
[root@master gpmall]# chmod +x /usr/local/bin/helm
[root@master gpmall]# helm version
version.BuildInfo{Version:"v3.8.0", GitCommit:"d14138609b01886f544b2025f5000351c9eb092e", GitTreeState:"clean", GoVersion:"go1.17.5"}
```

获取关于此项目参赛者的注册信息。

1. 登录到[GitLab](https://about.gitlab.com/" \t "https://www.alibabacloud.com/help/en/container-service-for-kubernetes/latest/_blank)控制台。
2. 在 GitLab 控制台的顶部导航栏中, 选择Projects > Your projects。
3. 在您的项目选项卡上，选择要使用的项目。
4. 在左侧导航栏中，选择Settings > CI / CD。
5. 单击Runners右侧的展开。
6. 复制 URL 和注册令牌。

![[1685589981714-0ecd35bf-956c-4e17-9746-bfd6c79fdd2e.png]]

```yaml
[root@master gpmall]# cd /root/gitlab-gpamll-cicd/gitlab-runner/
[root@master gitlab-runner]# vi templates/pvc.yaml
kind: PersistentVolumeClaim
metadata:
  annotations:
    volume.beta.kubernetes.io/storage-provisioner: alicloud/disk
  labels:
    app: {{ template "gitlab-runner.fullname" . }}
  name: gitlab-runner-cache
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: alicloud-disk-efficiency
status: {}
---
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv-sda
spec:
  capacity:
    storage: 20Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: alicloud-disk-efficiency
  local:
    path: /data/pv
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - master
```

编辑文件 values.yaml ：

```yaml
[root@master gitlab-runner]# vi values.yaml
image: gitlab/gitlab-runner:alpine-v11.7.0
imagePullPolicy: IfNotPresent
init:
  image: busybox
  tag: v1.0
gitlabUrl: "http://10.24.2.3:8888/"
runnerRegistrationToken: "LVJ-YbzYesSvJdhw-74e"
unregisterRunners: true
concurrent: 10
checkInterval: 30
rbac:
  create: true
  clusterWideAccess: false
metrics:
  enabled: true
runners:
  image: ubuntu:16.04
  tags: "k8s-runner"
  privileged: true
  namespace: demo
  cachePath: "/opt/cache"
  cache: {}
  builds: {}
  services: {}
  helpers: {}
resources: {}
```

打包 GitLab Runner 的 Helm 图表：

[root@master gitlab-runner]# helm package .

安装 GitLab Runner.

```bash
[root@master gitlab-runner]# helm install --namespace demo gitlab-runner gitlab-runner-0.1.37.tgz
```

检查相关的 Deployment 或 Pod 是否已经启动。如果相关的 Deployment 或 pods 已经启动，注册的 GitLab runner 会显示在 GitLab 中，如下图所示。

![[1685589982742-2178426b-9dc9-49d0-990c-5b11b7dca282.png]]

#### 2.4 安装 Harbor
```bash
[root@master gitlab-runner]# cd /root/gitlab-gpamll-cicd/
[root@master gitlab-gpamll-cicd]# /bin/bash install-harbor.sh
```

Harbor 创建项目

使用Harbor管理员或项目管理员帐户登录到Harbor（admin/Harbor12345）。

1. 转到项目并单击新建项目。
2. 提供项目的名称。
3. （可选）选中公开复选框以公开项目。

![[1685589983043-a4e13a95-5dff-4763-8cee-8b63741878b8.png]]

给镜像打标签

```bash
# docker load -i centos_centos7.5.1804.tar
# docker tag centos:centos7.5.1804 10.24.195.75/demo/centos:v7.5
# docker login 10.24.195.75 -u admin -p Harbor12345
# docker push 10.24.195.75/demo/centos:v7.5
# mkdir -p /data/pv/
```

#### 2.5 **Gitlab-Runner CI/CD**
设置全局变量

1. 在顶部导航栏中，选择Projects > Your projects**。**
2. 在您的项目选项卡上，选择要使用的项目。
3. 在左侧导航栏中，选择“设置> CI/CD ” 。
4. 单击变量右侧的展开。为 GitLab Runner 添加环境变量。在此示例中，添加以下变量：

![[1685589983499-965832d4-e335-421a-8438-587270a2a44a.png]]

REGISTRY_USERNAME：admin

REGISTRY_PASSWORD：Harbor12345

REGISTRY： " harbor IP "

kube_config：作为编码字符串的 KubeConfig。

运行以下命令将 KubeConfig 转换为编码字符串：

```bash
[root@master ~]# echo $(cat ~/.kube/config | base64) | tr -d " "
```

配置内置DNS服务器： 

[root@master ~]# kubectl get pods -A -owide

![[1685589983820-c15c643b-de1e-49a0-9bd7-605ba3472a2c.png]]

```bash
[root@master ~]# kubectl edit configmap coredns -n kube-system
```

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

###add

        hosts {

            10.244.0.53 gitlab-756655c6c-cjg2l  //pod ip/name

            10.24.2.36 [apiserver.cluster.local](https://apiserver.cluster.local:6443/api?timeout=32s)    //masterIP

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

使用.gitlab-ci.yml文件编译源代码，构建镜像，推送镜像，部署 Java 演示项目的应用程序。

以下模板是.gitlab-ci.yml文件的示例:

```yaml
stages:
  - mvn
  - nginx
  - db
  - zookeeper
  - kafaka
  - redis
  - k8s

variables:
  KUBECONFIG: /etc/deploy/config

maven_build:
  image: maven:3.6-jdk-8
  stage: mvn
  only:
    - drone
  tags:
    - k8s-runner
  script:
    - mkdir -p /root/.m2/repository && cp -rf repository /root/.m2/
    - ls && ls /root/.m2/ && ls /root/.m2/repository
    - cd /root/gpmall/maven/gpmall-parent/ &&  mvn clean package -Dmaven.test.skip=true
    - cd /root/gpmall/maven/gpmall-user/ && mvn clean package -Dmaven.test.skip=true
    - cd /root/gpmall/maven/user-service/ && mvn clean package -Dmaven.test.skip=true && ls
    - cp /root/gpmall/maven/gpmall-user/target/gpmall-user-0.0.1-SNAPSHOT.jar /opt/cache
    - cp /root/gpmall/maven/user-service/user-provider/target/user-provider-0.0.1-SNAPSHOT.jar /opt/cache

docker_build_redis:
  image: docker:v1.0
  stage: redis
  only:
    - drone
  tags:
    - k8s-runner
  script:
    - docker login -u $REGISTRY_USERNAME -p $REGISTRY_PASSWORD $REGISTRY
    - docker build -t $REGISTRY/demo/gpmall-redis:v1.0 -f Dockerfile-redis .
    - docker push  $REGISTRY/demo/gpmall-redis:v1.0
docker_build_db:
  image: docker:v1.0
  stage: db
  only:
    - drone
  tags:
    - k8s-runner
  script:
    - docker login -u $REGISTRY_USERNAME -p $REGISTRY_PASSWORD $REGISTRY
    - docker build -t $REGISTRY/demo/gpmall-mariadb:v1.0 -f Dockerfile-mariadb .
    - docker push $REGISTRY/demo/gpmall-mariadb:v1.0
docker_build_zookeeper:
  image: docker:v1.0
  stage: zookeeper
  only:
    - drone
  tags:
    - k8s-runner
  script:
    - docker login -u $REGISTRY_USERNAME -p $REGISTRY_PASSWORD $REGISTRY
    - docker build -t $REGISTRY/demo/gpmall-zookeeper:v1.0 -f Dockerfile-zookeeper .
    - docker push $REGISTRY/demo/gpmall-zookeeper:v1.0
docker_build_kafaka:
  image: docker:v1.0
  stage: kafaka
  only:
    - drone
  tags:
    - k8s-runner
  script:
    - docker login -u $REGISTRY_USERNAME -p $REGISTRY_PASSWORD $REGISTRY
    - docker build -t $REGISTRY/demo/gpmall-kafka:v1.0 -f Dockerfile-kafka .
    - docker push $REGISTRY/demo/gpmall-kafka:v1.0
docker_build_nginx:
  image: docker:v1.0
  stage: nginx
  only:
    - drone
  tags:
    - k8s-runner
  script:
    - cp -rf /opt/cache/*.jar /root/gpmall/nginx/
    - ls -l nginx
    - docker login -u $REGISTRY_USERNAME -p $REGISTRY_PASSWORD $REGISTRY
    - docker build -t $REGISTRY/demo/gpmall-nginx:v1.0 -f Dockerfile-nginx .
    - docker push $REGISTRY/demo/gpmall-nginx:v1.0
k8s:
  image: kubectl:1.16.6
  stage: k8s
  only:
    - drone
  tags:
    - k8s-runner
  script:
    - mkdir -p /etc/deploy
    - echo $kube_config |base64 -d > $KUBECONFIG
    - sed -i "s/REGISTRY/$REGISTRY/g" gpmall.yaml
    - kubectl apply -f gpmall.yaml
```

执行管道

提交.gitlab-ci.yml文件后，gitlab-java-demo 项目会自动检测该文件并执行管道，如下图所示。

![[1685589984137-5474f16b-730c-4e96-a377-08c7072c1d3b.png]]

使用浏览器访问 masterIP:30081 以访问应用程序。

![[1685589984628-813fd852-dab7-42bd-aa4e-556aeddd4974.png]]
