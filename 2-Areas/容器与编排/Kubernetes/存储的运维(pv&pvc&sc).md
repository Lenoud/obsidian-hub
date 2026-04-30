# 存储的运维（PV & PVC & SC）

存储的运维（PV & PVC & SC）相关技术笔记。

Kubernetes 中基于 NFS 的持久化存储管理，包括直接挂载和通过 PVC 方式挂载两种方式，以及 PV/PVC 绑定创建和回收策略处理。

## 挂载方式

### 1. 直接挂载 NFS

```yaml
    volumeMounts:
        - mountPath: /var/log/ #pod的目录
          name: nfs-filepath # pv
  volumes:
      - name: nfs-filepath #名称和上面的一致
        nfs:
          server: 192.168.90.218 #nfs服务器IP,不能使用域名
          path: k8s-data/podlog #不能使用type: DirectoryOrCreate 属性
```

### 2. 使用 PVC 方式挂载

```yaml
    volumeMounts:
        - mountPath: /var/log/
          name: mypvc
          subPath: FixLog
  volumes:
      - name: mypvc
        persistentVolumeClaim:
            claimName: pvcName #提前声明好的pvc
```

## NFS PV 与 PVC 绑定创建

PVC 绑定 PV 时 `storageClassName` 写成一样就行了。

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: gitlab-pv
spec:
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 15Gi
  nfs:
    path: /root/nfs-data/gitlab-data
    server: 192.168.100.101
  persistentVolumeReclaimPolicy: Retain
  storageClassName: nfs
  volumeMode: Filesystem
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: gitlab-conf
spec:
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 2Gi
  nfs:
    path: /root/nfs-data/gitlab-conf
    server: 192.168.100.101
  persistentVolumeReclaimPolicy: Retain
  storageClassName: nfs
  volumeMode: Filesystem
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: gitlab-log
spec:
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 1Gi
  nfs:
    path: /root/nfs-data/gitlab-log
    server: 192.168.100.101
  persistentVolumeReclaimPolicy: Retain
  storageClassName: nfs
  volumeMode: Filesystem
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: gitlab-data
  namespace: kube-ops
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 15G
  storageClassName: nfs
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: gitlab-conf
  namespace: kube-ops
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 2G
  storageClassName: nfs
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: gitlab-log
  namespace: kube-ops
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1G
  storageClassName: nfs
```

## 误删 PVC 后恢复 PV 状态

当回收策略为 Retain 不小心误删 PVC 导致 PV 状态变为 Released 时，让 PV 状态变成 Available 恢复 PVC 绑定 PV：

```bash
kubectl edit pv pvName
#我们大胆删除spec.claimRef这段。再次查看PV已经变为Available。
#重新创建之前的pvc即可重新绑定
```
