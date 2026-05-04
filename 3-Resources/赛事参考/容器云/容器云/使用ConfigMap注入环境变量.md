# 使用 ConfigMap 注入环境变量

通过 Kubernetes ConfigMap 将配置数据以环境变量方式注入 Pod 容器。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: exam-cfm
data:
  DB_HOST: localhost
  DB_PORT: "3306"
---
apiVersion: v1
kind: Pod
metadata:
  name: exam
spec:
  containers:
  - image: busybox
    name: exam
    command: ["/bin/sh","-c","echo $DB_HOST $DB_PORT"]
    envFrom:
    - configMapRef:
        name: exam-cfm
```

使用 `envFrom.configMapRef` 将 ConfigMap 中的所有键值对注入为容器环境变量。
