# KubeVirt 虚拟机实时迁移

使用 virtctl 命令将 KubeVirt 虚拟机实时迁移到另一个节点。

## 迁移命令

```bash
virtctl migrate exam-vm
```

## 查看迁移状态

```bash
kubectl get VirtualMachineInstanceMigration
kubectl get VirtualMachineInstanceMigration -o yaml
```

## 迁移资源示例

```yaml
apiVersion: kubevirt.io/v1
kind: VirtualMachineInstanceMigration
metadata:
  name: kubevirt-migrate-vm-8ghsp
  namespace: default
spec:
  vmiName: centos7
```
