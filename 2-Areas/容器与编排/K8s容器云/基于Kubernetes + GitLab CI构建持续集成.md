# 基于Kubernetes + GitLab CI构建持续集成

基于Kubernetes + GitLab CI构建持续集成相关技术笔记。

Kubernetes 容器编排相关笔记。

基于Kubernetes + GitLab CI

构建持续集成

**1 案例目标**

（1）掌握GitLab的部署流程和基本使用；

（2）熟悉GitLab Runner的部署流程和使用；

（3）掌握流水线脚本的语法。

**2 案例准备**

**2.1 规划节点**

节点规划，见表2-1-1。

表2-1-1节点规划

| **IP** | **主机名** | **节点** |
| :---: | :---: | :---: |
| 10.24.2.5 | master | Kubernetes master节点 |
| 10.24.2.6 | node | Kubernetes worker节点 |
| 10.24.2.10 | harbor | Harbor仓库 |

**2.2 基础准备**

Kubernetes集群和Harbor节点已部署完成，将提供的压缩包CICD-Runner.tar.gz上传到master节点/root目录下并解压。

**3 案例实施**

**3.1部署GitLab**

GitLab主要涉及到3个应用：Redis、Postgresql和Gitlab 核心程序。

**（1）基础准备**

所有节点解压软件包并导入镜像：

# tar -zxvf CICD-Runner.tar.gz

# docker load -i cicd-runner/images/image.tar

**（2）部署GitLab服务**

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
          value: admin123456
        - name: GITLAB_ROOT_EMAIL
          value: guorui@example.com
        - name: GITLAB_HOST
          value: 10.24.2.5  #masterIP
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
        - mountPath: /home/git/data
          name: data
      volumes:
      - name: data
        emptyDir: {}
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
```

创建资源：

```bash
[root@k8s-master-node1 manifests]# kubectl apply -f gitlab-deployment.yaml
namespace/kube-ops created
deployment.apps/gitlab created
service/gitlab created
```

查看Pod：

[root@k8s-master-node1 manifests]# kubectl -n kube-ops get pods

NAME                      READY   STATUS    RESTARTS   AGE

gitlab-8656b798ff-z54sm          1/1       Running    0            3m23s

**（3）自定义hosts**

查看GitLab Pod的IP地址：

[root@k8s-master-node1 manifests]# kubectl -n kube-ops get pods -owide

NAME    READY    STATUS    RESTARTS    AGE    IP    NODE    NOMINATED NODE    READINESS GATES

gitlab-8656b798ff-z54sm       1/1    Running    0    10m    10.244.1.87    k8s-worker-node1    <none>    <none>

在集群中自定义hosts添加gitlab Pod的解析：

```bash
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

        hosts {

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

**（4）访问GitLab**

查看Service：

[root@k8s-master-node1 manifests]# kubectl -n kube-ops get svc

NAME     TYPE     CLUSTER-IP   EXTERNAL-IP  PORT(S)            AGE

gitlab      NodePort   10.96.0.38    <none>    80:30880/TCP,22:32222/TCP   2m2s

通过http://master_ip:30880访问GitLab，如图所示：

![[1686464866946-0d87a6aa-2b94-47c3-809a-97a2d123415f.png]]

登录GitLab，如图所示：

![[1686464867297-95a64026-7091-4193-ab4f-466737422908.png]]

点击“创建一个项目”新建公开项目springcloud，如图所示：

![[1686464867669-869ac286-512f-4ede-ac77-07d2ade0501e.png]]

点击“Create project”完成创建，如图所示：

![[1686464868073-22b28112-eb18-4606-8269-2b848e215c26.png]]

**3.2 部署Gitlab CI Runner**

**（1）获取 Gitlab CI Register Token**

登录GitLab管理界面(http://master_ip:30880/admin)，然后点击左侧菜单栏中的Runners，如图所示：

![[1686464868409-f7077bc0-3a61-46a9-ac48-77da2ebf7cf9.png]]

可以看到该页面中有两个重要参数：Runner URL和Register Token，后期部署Runner时会用到这两个参数。W3--eHzAJsb2W1Uq_aUs

**（2）编写Gitlab CI Runner资源清单文件**

首先，通过ConfigMap资源来传递Runner镜像所需的环境变量，

[root@k8s-master-node1 manifests]# vi runner-configmap.yaml

```yaml
apiVersion: v1
data:
  REGISTER_NON_INTERACTIVE: "true" #修改
  REGISTER_LOCKED: "false"
  METRICS_SERVER: "0.0.0.0:9100"
  CI_SERVER_URL: "http://10.24.2.5:30880/"  #添加masterIP+port
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

编写一个用于注册、运行和取消注册Gitlab CI Runner的小脚本。只有当Pod正常通过 Kubernetes（TERM信号）终止时，才会触发转轮取消注册。如果强制终止Pod（SIGKILL信号），Runner将不会注销自身。必须手动完成对这种被杀死的Runner的清理，配置清单文件如下：

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

可以看到需要一个GITLAB_CI_TOKEN，然后使用Gitlab CI runner token来创建一个 Kubernetes secret对象。将token进行base64编码：

[root@k8s-master-node1 manifests]# echo MczWxUH3rLm4gMbVn9NN | base64 -w0

TWN6V3hVSDNyTG00Z01iVm45Tk4K

然后使用上面的token创建一个Secret对象：

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
  GITLAB_CI_TOKEN: TWN6V3hVSDNyTG00Z01iVm45Tk4K
```

接下来编写用于运行Runner的控制器对象，这里使用Statefulset资源对象。首先，在开始运行的时候，尝试取消注册所有的同名Runner，然后再尝试重新注册并开始运行。另外通过使用envFrom来指定Secrets和ConfigMaps来用作环境变量，对应的资源清单文件如下：

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

上面使用了一个名为gitlab-ci的serviceAccount，新建一个rbac资源清单文件：

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

**（3）部署GitLab CI Runner**

创建Runner资源对象：

[root@k8s-master-node1 manifests]# kubectl apply -f runner-configmap.yaml -f runner-scripts-configmap.yaml -f runner-token-secret.yaml -f runner-statefulset.yaml -f runner-rbac.yaml

configmap/gitlab-ci-runner-cm created

configmap/gitlab-ci-runner-scripts created

secret/gitlab-ci-token created

statefulset.apps/gitlab-ci-runner created

serviceaccount/gitlab-ci created

role.rbac.authorization.k8s.io/gitlab-ci created

rolebinding.rbac.authorization.k8s.io/gitlab-ci created

查看Pod：

[root@k8s-master-node1 manifests]# kubectl -n kube-ops get pods

NAME                 READY   STATUS    RESTARTS   AGE

gitlab-8656b798ff-z54sm      1/1       Running     0           32m

gitlab-ci-runner-0         1/1       Running     0           32s

gitlab-ci-runner-1         1/1       Running     0           85s

返回Runners页面并刷新，如图所示：

![[1686464868840-17a0a81a-556d-4b7f-b9a8-2fc8e05936d1.png]]

可以看到Runner已经注册成功。

**3.3 配置GitLab**

**（1）开启Container Registry**

在GitLab中开启Container Registry，进入springcloud项目，依次点击“设置”→“CI/CD”，如图所示：

![[1686464869274-d2c5d7ee-0dce-4246-b78b-bb60da5e3b03.png]]

展开“变量”栏目，配置镜像仓库相关的参数。

添加REGISTRY变量，值为Harbor仓库地址，如图所示：

![[1686464869600-484de2c3-8209-4020-9820-ffc68c8dba4c.png]]

然后继续添加变量

REGISTRY (10.24.2.5)

REGISTRY_IMAGE（springcloud）

REGISTRY_USER（admin）

REGISTRY_PASSWORD（Harbor12345）

REGISTRY_PROJECT（library）

HOST（10.24.2.5）

添加完成后保存变量，如图所示：

![[1686464870001-da8bb724-4ee9-4646-9e41-70a37cc15595.png]]

**（2）添加Kubernetes集群**

Kubectl在使用的时候默认会读取当前用户目录下面的~/.kube/config文件来链接集群，所以需要在Gitlab上配置集成Kubernetes集群。

在GitLab Admin界面下，点击“设置”，展开“外发请求”，勾选“允许钩子和服务访问本地网络”，如图所示：

![[1686464870305-91483e2e-b918-4b91-9df3-ac9a72e4ac1c.png]]

进入springcloud项目，依次点击左侧菜单栏“运维”→“Kubernetes”，如图所示：

![[1686464870623-e124f497-5c22-4142-ad3b-7e050cfa5dc5.png]]

点击“添加Kubernetes集群”，选择“Add existing cluster”，如图所示：

如果这里配置错误，平台不会报错，但是很有可能后面运行流水时会pod跑docker和k8s命令的时候会报错，一般是认证错误

![[1686464870971-3595be74-0132-46e4-8ed1-df3129b74f6c.png]]

Kubernetes 集群名称可以随便填写，如my-cluster。

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

[root@k8s-master-node1 manifests]# kubectl get serviceaccount gitlab -n gitlab -o json | jq -r '.secrets[0].name'

gitlab-token-z9ltb

然后根据上面的Secret找到CA证书：

[root@k8s-master-node1 manifests]# kubectl get secret gitlab-token-z9ltb -n gitlab -o json | jq -r '.data["ca.crt"]' | base64 -d

找到对应的Token：

[root@k8s-master-node1 manifests]# kubectl get secret gitlab-token-z9ltb -n gitlab -o json | jq -r '.data.token' | base64 -d

将获取到的值填入，如图所示：

![[1686464871354-457a1c7f-ea5d-42e4-8373-f0e6c85c5d6b.png]]

所有信息添加完成后如图所示：

![[1686464871743-814fd8e1-94b4-4d97-876b-ba2ee13fc060.png]]

点击“添加Kubernetes集群”，如图所示：

![[1686464872166-83f56263-f628-481c-b484-c1e2fd753795.png]]

**3.4 .gitlab-ci.yaml文件**

**（1）.gitlab-ci.yaml文件简介**

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

**（5）编写流水线脚本 **

编写.gitlab-ci.yaml

如果第三步，docker那报错了，可能是因为它去docker hub上下载包而dns无法解析，可以自己上传包到harbor，修改Dockerfile里的镜像名为上传的镜像名即可

[root@k8s-master-node1 manifests]# cd ../springcloud/

[root@k8s-master-node1 springcloud]# vi .gitlab-ci.yml

```yaml
image:
  name: yidaoyun/golang:v1.0
  entrypoint: ["/bin/sh", "-c"]
before_script:
  - mkdir -p "/go/src/$HOST/${CI_PROJECT_NAMESPACE}"
  - ln -sf "${CI_PROJECT_DIR}" "/go/src/$HOST/${CI_PROJECT_PATH}"
  - cd "/go/src/$HOST/${CI_PROJECT_PATH}/"
stages:
  - test
  - build
  - release
  - review
  - deploy
test:
  stage: test
  script:
    - make test
compile:
  stage: build
  script:
    - make build
  artifacts:
    paths:
      - app
image_build:
  stage: release
  image: yidaoyun/docker:v1.0
  variables:
    DOCKER_DRIVER: overlay
    DOCKER_HOST: tcp://localhost:2375
  services:
    - name: yidaoyun/docker-dind:v1.0
      command: ["--insecure-registry=0.0.0.0/0"]
  script:
    - docker login -u "${REGISTRY_USER}" -p "${REGISTRY_PASSWORD}" "${REGISTRY}"
    - docker build -t "${REGISTRY_IMAGE}:latest" .
    - docker tag "${REGISTRY_IMAGE}:latest" "${REGISTRY}/${REGISTRY_PROJECT}/${REGISTRY_IMAGE}:${CI_COMMIT_REF_NAME}"
    - test ! -z "${CI_COMMIT_TAG}" && docker push "${REGISTRY_IMAGE}:latest"
    - docker push "${REGISTRY}/${REGISTRY_PROJECT}/${REGISTRY_IMAGE}:${CI_COMMIT_REF_NAME}"
deploy_review:
  image: yidaoyun/kubectl:v1.0
  stage: review
  only:
    - branches
  except:
    - tags
  environment:
    name: dev
    on_stop: stop_review
  script:
    - cd manifests/
    - sed -i "s/__CI_ENVIRONMENT_SLUG__/${CI_ENVIRONMENT_SLUG}/" deployment.yaml service.yaml
    - sed -i "s|#IMAGE#|${REGISTRY}/${REGISTRY_PROJECT}/${REGISTRY_IMAGE}:${CI_COMMIT_REF_NAME}|g" deployment.yaml
    - |
      if kubectl apply -f deployment.yaml | grep -q unchanged; then
          kubectl patch -f deployment.yaml -p "{\"spec\":{\"template\":{\"metadata\":{\"annotations\":{\"ci-last-updated\":\"$(date +'%s')\"}}}}}"
      fi
    - kubectl apply -f service.yaml || true
    - kubectl rollout status -f deployment.yaml
stop_review:
  image: yidaoyun/kubectl:v1.0
  stage: review
  variables:
    GIT_STRATEGY: none
  when: manual
  only:
    - branches
  except:
    - master
    - tags
  environment:
    name: dev
    action: stop
  script:
    - kubectl delete ing -l ref=${CI_ENVIRONMENT_SLUG}
    - kubectl delete all -l ref=${CI_ENVIRONMENT_SLUG}
deploy_live:
  image: yidaoyun/kubectl:v1.0
  stage: deploy
  environment:
    name: live
  only:
    - tags
  when: manual
  script:
    - cd manifests/
    - sed -i "s/__CI_ENVIRONMENT_SLUG__/${CI_ENVIRONMENT_SLUG}/" deployment.yaml service.yaml
    - sed -i "s/__VERSION__/${CI_COMMIT_REF_NAME}/" deployment.yaml service.yaml
    - kubectl apply -f deployment.yaml
    - kubectl apply -f service.yaml
    - kubectl rollout status -f deployment.yaml
```

**3.5构建CICD**

**（1）触发构建**

推送代码到GitLab：

```bash
[root@k8s-master-node1 springcloud]# git config --global user.name "administrator"
[root@k8s-master-node1 springcloud]# git config --global user.email "admin@example.com"
[root@k8s-master-node1 springcloud]# git remote remove origin
[root@k8s-master-node1 springcloud]# git remote add origin http://10.24.2.5:30880/root/springcloud.git
[root@k8s-master-node1 springcloud]# git add .
[root@k8s-master-node1 springcloud]# git commit -m "initial commit"
[master 7a6ed09] initial commit
 1 file changed, 3 insertions(+), 3 deletions(-)
[root@k8s-master-node1 springcloud]# git push -u origin master
Username for 'http://10.24.2.5:30880': root
Password for 'http://root@10.24.2.5:30880':
```

Counting objects: 1299, done.

Delta compression using up to 8 threads.

Compressing objects: 100% (982/982), done.

Writing objects: 100% (1299/1299), 4.05 MiB | 0 bytes/s, done.

Total 1299 (delta 233), reused 1291 (delta 228)

remote: Resolving deltas: 100% (233/233), done.

To [http://10.24.2.5:30880/root/springcloud.git](http://10.24.2.5:30880/root/springcloud.git)

 * [new branch]      master -> master

Branch master set up to track remote branch master from origin.

当把仓库推送到GitLab以后，进入springcloud项目，依次点击“CI/CD”→“流水线”，可以看到GitLab CI开始执行构建任务了，如图所示：

![[1686464872504-50f63cbb-9d13-4ac9-aa63-35072dcf567a.png]]

点击  可查看构建详情，如图所示：

![[1686464872809-db3c337d-52b1-4c45-9583-b83f479e4cf6.png]]

点击流水线的任一阶段可查看构建详情，如图所示：

![[1686464873115-2452e81c-4db6-4800-b660-ea3d9dac817e.png]]

此时Runner Pod所在的namespace下面也会出现1个新的Pod：

[root@k8s-master-node1 springcloud]# kubectl -n kube-ops get pods

NAME                     READY      STATUS      RESTARTS      AGE

gitlab-8656b798ff-z54sm       1/1           Running      0               3h3m

gitlab-ci-runner-0             1/1           Running      0               168m

gitlab-ci-runner-1             1/1           Running      0               168m

runner-b2029df4-project-1-concurrent-0s7gcg     2/2      Running      0      25s

这个新Pod就是用来执行具体的Job任务的。

构建完成后如图所示：

![[1686464873463-7ac2ff9f-1807-490a-9189-2cde37aabf09.png]]

查看新发布的Pod：

[root@k8s-master-node1 manifests]# kubectl -n gitlab get pods

NAME                           READY   STATUS    RESTARTS   AGE

gitlab-k8s-demo-dev-789d4667-k6gjl   1/1     Running       0           15s

gitlab-k8s-demo-dev-789d4667-zprrj    1/1     Running       0           31s

**（2）查看Harbor**

登录Harbor仓库，进入library项目，如图所示：

![[1686464873793-31754ee3-451f-49c8-b2d5-a343363a68d1.png]]

可以看到镜像已构建并上传成功。

**（3）验证服务**

查看Service：

[root@k8s-master-node1 springcloud]# kubectl -n gitlab get svc

NAME       TYPE      CLUSTER-IP     EXTERNAL-IP   PORT(S)     AGE

gitlab-k8s-demo-dev   NodePort   10.96.145.19   <none>     8000:52427/TCP  11m

访问Service，如图所示：

![[1686464874070-a6487212-1e55-44e1-8ed2-4ade3ead4e21.png]]

遇到的问题   job3失败

原因：疏漏了变量  REGISTRY=192.168.100.35      Harbor仓库的设置

![[1686464874408-ec9df117-5a98-440a-ab46-e3f6877f1602.png]]

![[1686464874861-36090d6d-d8a4-49f6-9b8c-48977428e1d0.png]]

![[1686464875352-1f46a6e1-c233-4996-a47d-de06ff7789ab.png]]
