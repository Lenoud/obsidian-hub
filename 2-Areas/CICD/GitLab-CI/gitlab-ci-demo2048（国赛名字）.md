# GitLab CI 部署 demo2048（国赛）

GitLab CI 部署 demo2048（国赛）相关技术笔记。

使用 GitLab + GitLab Runner + GitLab Kubernetes Agent 在 K8s 集群上完成 demo2048 游戏应用的 CI/CD 流水线部署，涵盖 GitLab 部署、Runner 注册、Agent 配置及流水线编写。

## 部署 GitLab

```yaml
# cat app-gitlab.yaml
# kubectl apply -f app-gitlab.yaml -n gitlab-ci
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: gitlab
  name: gitlab
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gitlab
  strategy: {}
  template:
    metadata:
      labels:
        app: gitlab
    spec:
      nodeName: k8s-master-node1
      containers:
      - image: gitlab/gitlab-ce:latest
        name: gitlab-ce
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        volumeMounts:
        - name: log
          mountPath: /var/log/gitlab
        - name: data
          mountPath: /var/opt/gitlab
        - name: conf
          mountPath: /etc/gitlab
        env:
        - name: GITLAB_ROOT_PASSWORD
          value: lB781023123456
      volumes:
      - name: log
        hostPath:
          path: /home/gitlab/log/
      - name: data
        hostPath:
          path: /home/gitlab/data
      - name: conf
        hostPath:
          path: /home/gitlab/conf
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: gitlab
  name: gitlab
spec:
  ports:
  - name: "80"
    nodePort: 30880
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: gitlab
  type: NodePort
```

## RBAC 权限配置

```yaml
# cat admin.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: gitlab
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: default
  namespace: gitlab-ci
```

```yaml
# cat user.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: gitlab-ci
  namespace: gitlab-ci
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: gitlab
  namespace: gitlab-ci
rules:
- apiGroups:
  - "*"
  resources:
  - '*'
  verbs:
  - '*'
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: gitlab-ci
  namespace: gitlab-ci
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: gitlab
subjects:
- kind: ServiceAccount
  name: gitlab-ci
  namespace: gitlab-ci
```

## 推送代码到 GitLab

```bash
git config --global user.name "Administrator"
git config --global user.email "admin@example.com"
git init .
git add .
git branch
git remote
git remote  remove origin
git remote add origin  http://192.168.100.20:30880/root/demo-2048.git
git commit -m "push file"
git push --set-upstream origin drone
```

> 记得开启钩子访问，如果 GitLab 部署时无故重启，可能是 root 密码太弱。

## 部署 GitLab Runner

```yaml
# [root@k8s-master-node1 gitlab-runner]# cat values.yaml |grep ^[^#]|grep -Ev "#"
image:
  registry: registry.gitlab.com
  image: gitlab-org/gitlab-runner
#  tag: alpine-v15.2.0
imagePullPolicy: IfNotPresent
#replicas: 1
#gitlabUrl: http://192.168.100.20:30880/
#runnerRegistrationToken: "a4fniw7Egu2PFanZx7SM"
terminationGracePeriodSeconds: 3600
concurrent: 10
checkInterval: 30
sessionServer:
  enabled: false
rbac:
  create: false
  rules: []
  clusterWideAccess: false
  podSecurityPolicy:
    enabled: false
    resourceNames:
    - gitlab-runner
metrics:
  enabled: false
  portName: metrics
  port: 9252
  serviceMonitor:
    enabled: false
service:
  enabled: false
  type: ClusterIP
runners:
  config: |
    [[runners]]
      [runners.kubernetes]
        namespace = "{{.Release.Namespace}}"
        image = "ubuntu:16.04"
 #       privileged = true
  cache: {}
  builds: {}
  services: {}
  helpers: {}
#  serviceAccountName: gitlab-ci
securityContext:
  allowPrivilegeEscalation: false
  readOnlyRootFilesystem: false
  runAsNonRoot: true
  privileged: false
  capabilities:
    drop: ["ALL"]
podSecurityContext:
  runAsUser: 100
  fsGroup: 65533
resources: {}
affinity: {}
nodeSelector: {}
tolerations: []
hostAliases: []
podAnnotations: {}
podLabels: {}
priorityClassName: ""
secrets: []
configMaps: {}
volumeMounts: []
volumes: []
#cibuild:
#  cache:
#    pvcName: build-pv
#    mountPath: /home/gitlab-runner/ci-build-cache
```

## PVC 持久化缓存

```yaml
# cat gitlab-runner/templates/pvc.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: build-pv
  namespace: gitlab-ci
spec:
  storageClassName: build
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 5Gi
  hostPath:
    path: /opt/ci-build-cache
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: build-pv
  namespace: gitlab-ci
spec:
  storageClassName: build
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```

```yaml
# [root@k8s-master-node1 gitlab-ci]# cat gitlab-runner/templates/configmap.yaml |grep -B 10 Start
#查找Start字符串，在上方插入内容
    if ! bash /configmaps/pre-entrypoint-script; then
      exit 1
    fi
 #   cat >> /home/gitlab-runner/.gitlab-runner/config.toml << EOF
 #   [[runners.kubernetes.volumes.pvc]]
 #   name = "{{.Values.cibuild.cache.pvcName}}"
 #   mount_path = "{{.Values.cibuild.cache.mountPath}}"
 #   EOF

    # Start the runner
```

随后使用 Helm 创建 GitLab Runner，选择 `gitlab-ci` 名称空间即可。

## 部署 GitLab Kubernetes Agent

在 `demo-2048` 项目创建 `.gitlab/agents/kubernetes-agent` 文件夹和 `.gitlab/agents/kubernetes-agent/config.yaml` 文件，否则在 `kubernetes-slusters` 中无法选择。

随后在左侧 `operate` 选择 `kubernetes-slusters` 创建集群。

使用它提供的 Helm 命令修改名称空间和对应的 URL 和目录，创建 `kubernetes-agent`：

```bash
helm upgrade --install kubernetes-agent /root/gitlab-agent \
  --namespace gitlab-ci \
  --create-namespace \
  --set image.tag=v16.2.0 \
  --set config.token=tzHfTaJjRWHrtpDhdiHsazHPgqzz8fiE-zs2nncjsZeV3ZZbsg \
  --set config.kasAddress=ws://10.244.0.244/-/kubernetes-agent/
```

> `10.244.0.244` 为 GitLab Pod 的 IP 地址。

## 流水线部署阶段

在 `demo-2048` 的 CI/CD 中找到 `Variables` 配置对应的环境变量：

| **Key** | **Value** |
| :--- | :--- |
| HOST | 192.168.100.20 |
| REGISTRY | 192.168.100.20 |
| REGISTRY_IMAGE | demo |
| REGISTRY_NAME | 192.168.100.20 |
| REGISTRY_PASSWORD | Harbor12345 |
| REGISTRY_PROJECT | demo |
| REGISTRY_USER | admin |

> 注意：Dockerfile 中 `192.168.100.20/library/tomcat:8.5.64-jdk8` 容器镜像必须在 Harbor 中实际存在。

```dockerfile
#FROM 192.168.100.20/library/tomcat:8.5.64-jdk8
RUN rm -rf /usr/local/tomcat/webapps/ROOT/
ADD 2048 /usr/local/tomcat/webapps/ROOT/
```

流水线脚本可以在失败的历史流水线中找到 `Delete .gitlab-ci.yml`，点击 `.gitlab-ci.yml` 会有流水线的完整配置，稍作修改即可使用。

```yaml
stages:
  - build
  - release
  - review

variables:
  MAVEN_OPTS: "-Dmaven.repo.local=/opt/cache/.m2/repository"

maven_build:
  image: maven:3.6-jdk-8
  stage: build
  only:
    - drone
  script:
    - cp -r /opt/repository /opt/cache/.m2/
    - mvn clean install -DskipTests=true
    - cd target && jar -xf 2048.war
    - mkdir -p /home/gitlab-runner/ci-build-cache/
    - cp -rfv 2048 /home/gitlab-runner/ci-build-cache
##
image_build:
  image: docker:18.09.7
  stage: release
  variables:
    DOCKER_DRIVER: overlay2
    DOCKER_HOST: tcp://localhost:2375

  services:
    - name: docker:18.09.7-dind
      entrypoint: ["dockerd-entrypoint.sh"]
#      command: ["--insecure-registry", "0.0.0.0/0"]
  script:
    #- docker login -u "${REGISTRY_USER}" -p "${REGISTRY_PASSWORD}" "${REGISTRY}"
  #注意我这里没有登录harbor，因为我使用的registry容器不需要登录，如果有harbor需要配置环境变量和对应的命令
    - cp -rfv /home/gitlab-runner/ci-build-cache/2048 .
#    - sed -i "s/192.168.100.20/$REGISTRY/g" ./Dockerfiles/Dockerfile
    - docker build -t "${REGISTRY_IMAGE}:latest" -f ./Dockerfiles/Dockerfile .
    - docker tag "${REGISTRY_IMAGE}:latest" "${REGISTRY}/${REGISTRY_PROJECT}/${REGISTRY_IMAGE}:latest"
    - docker push "${REGISTRY}/${REGISTRY_PROJECT}/${REGISTRY_IMAGE}:latest"

deploy_review:
  image: kubectl:1.22
  stage: review
  only:
    - drone
  script:
    - sed -i "s/REGISTRY/$REGISTRY/g" template/demo-2048.yaml
    - kubectl apply -f template/
```
