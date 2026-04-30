# owncloud网盘

owncloud网盘相关技术笔记。

k8s使用mysql作为后端

```python
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: owncloud
  name: owncloud
spec:
  replicas: 1
  selector:
    matchLabels:
      app: owncloud
  template:
    metadata:
      labels:
        app: owncloud
    spec:
      containers:
      - image: owncloud
        name: owncloud
        ports:
        - containerPort: 80
        imagePullPolicy: IfNotPresent
#/var/www/html/data持久化存储
      - image: mysql:5.6
        name: mysql
        ports:
        - containerPort: 3306
        imagePullPolicy: IfNotPresent
        env:
        - name: MYSQL_USER
          value: owncloud
        - name: MYSQL_PASSWORD
          value: owncloud
        - name: MYSQL_DATABASE
          value: owncloud
        - name: MYSQL_ROOT_PASSWORD
          value: owncloud
#/var/lib/mysql持久化存储
---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: own-svc
  name: owncloud-svc
spec:
  ports:
  - name: "80"
    nodePort: 30003
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: owncloud
  type: NodePort
```

```python
version: '3.0'
services:
  owncloud:
    image: owncloud
    ports:
      - "80:80"
    restart: always
    volumes:
      - ./owddata:/var/www/html/data

  mysql:
    image: mysql:5.7
    ports:
      - "3306:3306"
    environment:
      MYSQL_USER: owncloud
      MYSQL_PASSWORD: owncloud
      MYSQL_DATABASE: owncloud
      MYSQL_ROOT_PASSWORD: owncloud
    restart: always
    volumes:
      - ./dbdata:/var/www/html/data
```
