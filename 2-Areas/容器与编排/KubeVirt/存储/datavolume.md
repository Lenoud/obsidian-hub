# DataVolume

DataVolume相关技术笔记。

KubeVirt CDI（Containerized Data Importer）提供的 DataVolume 资源，用于从现有 PVC 克隆数据创建新的持久化卷。

```yaml
apiVersion: cdi.kubevirt.io/v1beta1
kind: DataVolume
metadata:
  name: my-datavolume
spec:
  source:
    pvc:
      name: pvc
      namespace: default
  pvc:
    accessModes: ["ReadWriteOnce"]
    resources:
      requests:
        storage: 5Gi
```
