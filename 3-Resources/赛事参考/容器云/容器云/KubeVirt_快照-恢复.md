# KubeVirt 快照与恢复

使用 KubeVirt 的 VirtualMachineSnapshot 和 VirtualMachineRestore 资源对虚拟机进行快照创建和恢复操作。

## 创建快照

快照名称：exam-snap，创建超时时间：1min。

```yaml
apiVersion: snapshot.kubevirt.io/v1alpha1
kind: VirtualMachineSnapshot
metadata:
  name: exam-snap
spec:
  source:
    apiGroup: kubevirt.io
    kind: VirtualMachine
    name: exam
  failureDeadline: 1m
```

## 恢复快照

必须先关闭虚拟机，再执行恢复：

```bash
kubectl wait vmrestore restore-exam --for condition=Ready
```

恢复模板：

```yaml
apiVersion: snapshot.kubevirt.io/v1alpha1
kind: VirtualMachineRestore
metadata:
  name: restore-exam
spec:
  target:
    apiGroup: kubevirt.io
    kind: VirtualMachine
    name: exam-vm
  virtualMachineSnapshotName: snap-exam
```

## RBAC 角色配置

角色名称：vm-role，对 VM 拥有 get、delete、create、update、patch 和 list 权限。

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: vm-role
rules:
- apiGroups: ["kubevirt.io"]
  resources: ["virtualmachines"]
  verbs: ["get", "delete", "create", "update", "patch", "list"]
```
