# KubeVirt DataVolume 热插拔

使用 KubeVirt DataVolume 为运行中的虚拟机动态添加存储卷。

## 创建 DataVolume

```yaml
apiVersion: cdi.kubevirt.io/v1alpha1
kind: DataVolume
metadata:
  name: volume-hotplug-datavolume
spec:
  pvc:
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 5Gi
    volumeMode: Filesystem
```

## 热插拔到虚拟机

```bash
virtctl volume-upload --namespace default --name volume-hotplug --mount-path /data vm-exam
```
