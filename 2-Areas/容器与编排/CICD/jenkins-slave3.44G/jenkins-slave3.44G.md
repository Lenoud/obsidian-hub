# Jenkins Slave 部署（基于 NFS 存储）

Jenkins Slave 部署（基于 NFS 存储）相关技术笔记。

使用 NFS 作为后端存储，在 Kubernetes 上部署 Jenkins Master，通过挂载宿主机的 Docker 和 kubectl 实现容器内构建与部署能力。

## 说明

| 组件 | 存储类型 | Service 类型 | 端口映射 |
| :--- | :--- | :--- | :--- |
| Jenkins | NFS PVC | NodePort | 8080 -> 30880 |

## 创建 NFS 存储目录

```bash
# /etc/exports 添加：
/data/k8s  192.168.100.0/24(rw)
mkdir /data/k8s
exportfs -r && exportfs
chmod 777 -R /data/k8s
```

## 创建 PV 和 PVC

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv
spec:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 5Gi
  storageClassName: pvc-storage
  nfs:
    server: 192.168.100.20
    path: /data/k8s/
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc
spec:
  storageClassName: pvc-storage
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## 部署 Jenkins

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: jenkins
  name: jenkins
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  strategy: {}
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      containers:
      - image: jenkins/jenkins:latest
        name: jenkins
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 8080
        volumeMounts:
        - name: jenkinshome
          mountPath: /var/jenkins_home/
        - name: dockersock
          mountPath: /var/run/docker.sock
        - name: docker
          mountPath: /usr/bin/docker
        - name: kubectl
          mountPath: /usr/bin/kubectl
        - name: kubeconfig
          mountPath: /root/.kube/
        securityContext:
          runAsUser: 0
          privileged: true
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
          path: /root/.kube/
---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: jenkins
  name: jenkins
spec:
  ports:
  - name: "http"
    nodePort: 30880
    port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    app: jenkins
  type: NodePort
```
