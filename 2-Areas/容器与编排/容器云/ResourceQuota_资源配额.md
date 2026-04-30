# ResourceQuota 资源配额

使用 Kubernetes ResourceQuota 限制命名空间中的资源使用总量，包括存储、CPU、内存和 Pod 数量。

## 存储配额

ResourceQuota 名称：storagequota，限制命名空间 exam 的 PVC 数量为 10，累计存储容量为 20Gi。

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: storagequota
  namespace: exam
spec:
  hard:
    persistentvolumeclaims: 10
    requests.storage: 20Gi
```

```bash
kubectl get resourcequota -n exam
# persistentvolumeclaims: 10：限制 PVC 数量为 10 个
# requests.storage: 20Gi：限制总存储容量为 20Gi
```

## 计算资源配额

ResourceQuota 名称：compute-resources，限制 Pod 数量、内存和 CPU。

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-resources
  namespace: compute-resources
spec:
  hard:
    limits.cpu: "2"
    limits.memory: 2Gi
    requests.cpu: "1"
    requests.memory: 1Gi
    persistentvolumeclaims: 4
```
