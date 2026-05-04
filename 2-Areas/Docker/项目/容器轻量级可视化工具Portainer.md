# 容器轻量级可视化工具Portainer

容器轻量级可视化工具Portainer相关技术笔记。

## docker部署
```yaml
version: "3"
services:
  portainer:
    image: portainer/portainer-ce:2.21.3
    container_name: portainer
    ports:
      - 9000:9000
    network_mode: bridge
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./data:/data
    restart: always
```

## k8s部署
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: portainer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: portainer
  name: portainer
  labels:
    app: portainer
spec:
  replicas: 1
  selector:
    matchLabels:
      app: portainer
  template:
    metadata:
      labels:
        app: portainer
    spec:
      containers:
      - name: portainer
        image: portainer/portainer-ce:2.20.1-alpine
        ports:
        - containerPort: 9000
        volumeMounts:
        - name: docker-socket
          mountPath: /var/run/docker.sock
        - name: data-volume
          mountPath: /data
      volumes:
      - name: docker-socket
        hostPath:
          path: /var/run/docker.sock
      - name: data-volume
        hostPath:
          path: /home/luobozi/portainer/data
---
apiVersion: v1
kind: Service
metadata:
  namespace: portainer
  name: portainer
spec:
  selector:
    app: portainer
  ports:
    - protocol: TCP
      port: 9000
      targetPort: 9000
      nodePort: 9000
  type: NodePort

```

## 远程监控k8s
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: portainer
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: portainer-sa-clusteradmin
  namespace: portainer
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: portainer-crb-clusteradmin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: portainer-sa-clusteradmin
  namespace: portainer
---
apiVersion: v1
kind: Service
metadata:
  name: portainer-agent
  namespace: portainer
spec:
#  type: NodePort
  selector:
    app: portainer-agent
  ports:
    - name: http
      protocol: TCP
      port: 9001
      targetPort: 9001
      #nodePort: 30999
---
apiVersion: v1
kind: Service
metadata:
  name: portainer-agent-headless
  namespace: portainer
spec:
  clusterIP: None
  selector:
    app: portainer-agent
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: portainer-agent
  namespace: portainer
spec:
  selector:
    matchLabels:
      app: portainer-agent
  template:
    metadata:
      labels:
        app: portainer-agent
    spec:
      serviceAccountName: portainer-sa-clusteradmin
      containers:
      - name: portainer-agent
        image: portainer/agent:2.20.1
        imagePullPolicy: Always
        env:
        - name: LOG_LEVEL
          value: DEBUG
        - name: AGENT_CLUSTER_ADDR
          value: "portainer-agent-headless"
        - name: KUBERNETES_POD_IP
          valueFrom:
            fieldRef:
              fieldPath: status.podIP
        ports:
        - containerPort: 9001
          protocol: TCP

```

## 远程监控docker
```yaml
version: '3.0'
services:
    agent:
        image: 'portainer/agent:2.20.1'
        volumes:
            - '/var/lib/docker/volumes:/var/lib/docker/volumes'
            - '/var/run/docker.sock:/var/run/docker.sock'
        restart: always
        container_name: portainer_agent
        ports:
            - '9001:9001'

```

# 参考博客
[https://blog.csdn.net/qq_35716085/article/details/135268224](https://blog.csdn.net/qq_35716085/article/details/135268224)
