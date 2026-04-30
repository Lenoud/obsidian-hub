# Helm Chart 模板开发

Helm Chart 模板开发相关技术笔记。

自定义一个 nginx-deployment 的 Helm Chart 模板，包含 Chart.yaml 和 Deployment 模板文件的基本结构。

## Chart 目录结构

```
nginx-chart/
├── Chart.yaml
└── templates/
    └── nginx-deploy.yaml
```

一个目录两个文件即可创建一个基础的 Chart 模板。

## Deployment 模板

```yaml
# nginx-chart/templates/nginx-deploy.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-deployment  #helm创建时传递的名称
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx-container
          imagePullPolicy: IfNotPresent
          image: nginx:latest
          ports:
            - containerPort: 80
```

## Chart 元数据

```yaml
# nginx-chart/Chart.yaml
apiversion: v1
name: nginx-deploy
version: 0.1.0
#必要的元数据信息
```
