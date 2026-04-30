# K8s 部署 Deployment 形式的 WordPress

K8s 部署 Deployment 形式的 WordPress相关技术笔记。

使用 Kubernetes Deployment 部署 WordPress + MySQL 应用，WordPress 与 MySQL 运行在同一个 Pod 中共享网络命名空间，通过 NodePort 方式对外暴露服务。

## 说明

| 组件 | 镜像 | 端口 | 环境变量 |
| :--- | :--- | :--- | :--- |
| WordPress | `wordpress` | 80 | WORDPRESS_DB_HOST, WORDPRESS_DB_USER, WORDPRESS_DB_PASSWORD, WORDPRESS_DB_NAME |
| MySQL | `mysql:5.7` | 3306 | MYSQL_USER, MYSQL_PASSWORD, MYSQL_DATABASE, MYSQL_ROOT_PASSWORD |

## Deployment 与 Service YAML

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
  labels:
    app: wordpress
  name: wordpress
spec:
  ports:
  - port: 80
    protocol: TCP
    nodePort: 30000
  selector:
    app: wordpress
  type: NodePort

```
