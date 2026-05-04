# 存储的运维(pv&pvc&sc)

存储的运维(pv&pvc&sc)相关技术笔记。

需要用nfs方式，挂载远程目录到pod中，挂载方式有：

1.  直接挂载

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

2. 使用pvc方式挂载

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

nfs pv pvc绑定创建

pvc绑定pv时 storageClassName 写成一样就行了

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

当回收策略为 Retain 不小心误删pvc导致pv状态变为Retain ，让PV状态变成Available恢复pvc绑定pv

```yaml
kubectl edit pv pvName
#我们大胆删除spec.claimRef这段。再次查看PV已经变为Available。
#重新创建之前的pvc即可重新绑定
```
