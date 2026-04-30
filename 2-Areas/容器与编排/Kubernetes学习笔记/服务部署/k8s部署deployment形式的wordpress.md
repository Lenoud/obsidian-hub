# k8s部署deployment形式的wordpress

k8s部署deployment形式的wordpress相关技术笔记。

Kubernetes 容器编排相关笔记。

本文将wordpress和mysql部署在同一pod中

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: wordpress
  name: wordpress
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: wordpress
    spec:
      containers:
      - image: wordpress
        name: wordpress
        env:
        - name: WORDPRESS_DB_HOST
          value: 127.0.0.1
          #因为mysql和wordpress部署在同一pod中所以它们共用一个IP，这里直接用回环地址
        - name: WORDPRESS_DB_USER
          value: wordpress
        - name: WORDPRESS_DB_PASSWORD
          value: wordpress
        - name: WORDPRESS_DB_NAME
          value: wordpress
      - image: mysql:5.7
        name: mysql
        env:
        - name: MYSQL_USER
          value: wordpress
        - name: MYSQL_PASSWORD
          value: wordpress
        - name: MYSQL_DATABASE
          value: wordpress
        - name: MYSQL_ROOT_PASSWORD
          value: "000000"
---
#编写一个svc映射wordpress的80端口到本级的666端口
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: wordpress
  name: wordpress
spec:
  ports:
  - port: 80
    protocol: TCP
    nodePort: 6666
  selector:
    app: wordpress
  type: NodePort
```
