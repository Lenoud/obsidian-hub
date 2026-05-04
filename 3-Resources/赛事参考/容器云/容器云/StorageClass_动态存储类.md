# StorageClass 动态存储类

使用 Kubernetes StorageClass 定义动态存储供给策略，支持 NFS、Ceph 等多种存储后端。

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nfs-storage
provisioner: kubernetes.io/nfs
parameters:
  nfsServer: 192.168.100.20
  nfsPath: /data/k8s
```
