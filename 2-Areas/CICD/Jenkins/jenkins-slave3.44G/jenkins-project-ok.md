# Jenkins 项目部署（已完成验证）

Jenkins 项目部署（已完成验证）相关技术笔记。

在 Kubernetes 上部署 Jenkins 及 Docker-in-Docker（DinD）的完整 YAML 清单，包含 RBAC 权限、NFS 持久化存储和 Docker 服务的配置。

## 说明

| 组件 | 命名空间 | 存储类型 | Service 类型 | 端口 |
| :--- | :--- | :--- | :--- | :--- |
| Jenkins | jenkins | NFS PVC | NodePort | 8080 -> 30880, 50000 -> 30500 |
| Docker DinD | jenkins | NFS PVC | ClusterIP | 2375 |

## Jenkins RBAC 与部署

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins-admin
  namespace: jenkins
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: jenkins-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: jenkins-admin
  namespace: jenkins
#以上是创建了一个名为jenkins-admin的角色，并且绑定了集群管理员权限
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: jenkins
  name: jenkins
  namespace: jenkins
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      containers:
      - image: jenkins/jenkins:latest
        imagePullPolicy: IfNotPresent
        name: jenkins
        ports:
        - containerPort: 8080
          name: web
        - containerPort: 50000
          name: http
        securityContext:
          privileged: true
          runAsUser: 0
        volumeMounts:
        - mountPath: /var/jenkins_home
          name: jenkinshome
      nodeName: k8s-master-node1
      serviceAccountName: jenkins-admin
      volumes:
      - name: jenkinshome
        persistentVolumeClaim:
          claimName: jenkins-pvc
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: jenkins-pvc
  namespace: jenkins
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
  storageClassName: nfs-storage
#持久化存储jenkinshome数据
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: jenkins
  name: jenkins
  namespace: jenkins
spec:
  ports:
  - name: web
    nodePort: 30880
    port: 8080
    protocol: TCP
    targetPort: 8080
  - name: agent
    nodePort: 30500
    port: 50000
    protocol: TCP
    targetPort: 50000
  selector:
    app: jenkins
  type: NodePort
```

## Docker-in-Docker 部署

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  labels:
    app: docker-dind
  name: docker-dind-data
  namespace: jenkins
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: nfs-storage
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: docker-dind
  name: docker-dind
  namespace: jenkins
spec:
  selector:
    matchLabels:
      app: docker-dind
  template:
    metadata:
      labels:
        app: docker-dind
    spec:
      containers:
      - env:
        - name: DOCKER_HOST
          value: tcp://0.0.0.0:2375
        - name: DOCKER_TLS_CERTDIR
          value: ""
        image: docker:dind
        imagePullPolicy: IfNotPresent
        name: docker-dind
        ports:
        - containerPort: 2375
          name: daemon-port
        securityContext:
          privileged: true
        volumeMounts:
        - mountPath: /var/lib/docker/
          name: docker-dind-data-vol
      volumes:
      - name: docker-dind-data-vol
        persistentVolumeClaim:
          claimName: docker-dind-data
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: docker-dind
  name: docker-dind
  namespace: jenkins
spec:
  ports:
  - port: 2375
    targetPort: 2375
  selector:
    app: docker-dind
```

## 配置 Jenkins 连接 Kubernetes

按照上述 YAML 部署完成后，在 Jenkins 系统管理中配置 Kubernetes 连接即可。
