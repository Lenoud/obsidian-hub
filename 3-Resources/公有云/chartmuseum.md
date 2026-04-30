# ChartMuseum 私有 Helm 仓库部署

使用 Kubernetes 部署 ChartMuseum 私有 Chart 仓库，支持本地存储和 API 推送。

## 部署

创建 Deployment 和 Service：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: chartmuseum
  labels:
    app: chartmuseum
spec:
  replicas: 1
  selector:
    matchLabels:
      app: chartmuseum
  template:
    metadata:
      labels:
        app: chartmuseum
    spec:
      containers:
      - name: chartmuseum
        image: chartmuseum/chartmuseum
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 8080
        volumeMounts:
        - name: charts
          mountPath: /charts
        args:
        - "--storage=local"
        - "--storage-local-rootdir=/charts"
      volumes:
      - name: charts
        hostPath:
          path: /data/charts
---
apiVersion: v1
kind: Service
metadata:
  name: chartmuseum
spec:
  selector:
    app: chartmuseum
  ports:
    - name: http
      port: 8080
      targetPort: 8080
  type: ClusterIP
```

## 推送 Chart 包

```bash
chmod 777 /data/charts
curl --data-binary "@wordpress-13.0.23.tgz" http://10.247.97.1:8080/api/charts
```
