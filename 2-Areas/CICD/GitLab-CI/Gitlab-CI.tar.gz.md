# GitLab CI 快速部署指南

GitLab CI 快速部署指南相关技术笔记。

使用 YAML 清单在 Kubernetes 上快速部署 GitLab CE、GitLab Runner 和 GitLab Agent 的完整流程。

## 说明

| 组件 | 镜像 | Service 类型 | 端口映射 |
| :--- | :--- | :--- | :--- |
| GitLab CE | `gitlab/gitlab-ce:latest` | NodePort | 80 -> 30880 |
| GitLab Runner | `registry.gitlab.com/gitlab/gitlab-runner:latest` | - | - |
| GitLab Agent | `registry.gitlab.com/gitlab-org/cluster-integration/gitlab-agent/agentk:v15.1.0` | - | - |

## 1. 部署 GitLab

```yaml
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
        - name: gitlabdata
          mountPath: /var/opt/gitlab/
      volumes:
      - name: gitlabdata
        hostPath:
          path: /data/gitlab/
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: gitlab
  name: gitlab
spec:
  ports:
  - name: http
    nodePort: 30880
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: gitlab
  type: NodePort
```

## 2. 部署 GitLab Runner

```yaml
image:
  registry: registry.gitlab.com
  image: gitlab/gitlab-runner
  tag: latest
imagePullPolicy: IfNotPresent
replicas: 2
gitlabUrl: http://192.168.100.20:30880/
runnerRegistrationToken: "7T9mDwFX1BejxtHK6TrW"
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
  cache:
    cacheType: s3
    cachePath: "/home/gitlab-runner/ci-build-cache"
    cacheShared: true
  builds: {}
  services: {}
  helpers: {}
securityContext:
  runAsUser: 0
  privileged: true
  capabilities:
    add: ["ALL"]
podSecurityContext:
  runAsUser: 0
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
```

## 3. 部署 GitLab Agent

```yaml
image:
  repository: "registry.gitlab.com/gitlab-org/cluster-integration/gitlab-agent/agentk"
  pullPolicy: IfNotPresent
  tag: "v15.1.0"
imagePullSecrets: []
nameOverride: ""
fullnameOverride: ""
rbac:
  create: true
serviceAccount:
  create: true
  annotations: {}
podAnnotations:
  prometheus.io/scrape: "true"
  prometheus.io/path: "/metrics"
  prometheus.io/port: "8080"
config:
  kasAddress: 'ws://gitlab-59c4f599d5-khbqz/-/kubernetes-agent/'
  token: "n7JEtmTxaKzS8-zNkoiGXf6s3fZSsPiwGzV3PgNpPxtRuBcCpQ"
extraEnv: []
resources: {}
nodeSelector: {}
tolerations: []
affinity: {}
```
