# WordPress Helm Chart 部署

使用 wordpress-13.0.23.tgz Chart 包部署 WordPress 服务，创建所需 PV 并修改 Service 类型为 NodePort。

## 修改 values.yaml

将 Service 类型改为 NodePort，存储类改为 manual：

```bash
# 修改 Service 类型
grep -n "type" values.yaml
# 541:  type: NodePort

# 修改存储类
grep -n "storageClass" values.yaml
# 723:  storageClass: "manual"
# 1131:  storageClass: "manual"
```

## 创建 PV

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: wordpress-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: /data/wordpress-pv
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-wordpress-mariadb-0
spec:
  capacity:
    storage: 8Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: /data/db
```

## 安装

```bash
kubectl apply -f pv.yaml
helm install wordpress .
```
