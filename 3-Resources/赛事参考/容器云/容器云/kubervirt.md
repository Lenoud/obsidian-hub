# KubeVirt 动态密钥注入

使用 KubeVirt 的 qemuGuestAgent 为虚拟机动态注入 SSH 公钥。

基于 master 节点的 id_rsa.pub 创建 Secret，注入到 VM 中。

（2）使用qemuGuestAgent在VM上引用这个Secret。

```yaml
#kubectl create secret generic my-pub-key \
#--from-file ~/.ssh/id_rsa.pub --dry-run -oyaml
apiVersion: v1
data:
  id_rsa.pub: c3NoLXJzYSBBQUFBQjNOemFDMXljMkVBQUFBREFRQUJBQUFCQVFDNnFzT1N6ZnNsVUM0ZFFSaXJ6YWoyTzNoY09QM09PVVB1Nms0akxXa0IrOEtGVS92QWFscVBFTktocU91YlhzaGFBSUc2bTU3NVVKdGxWZFFqTlJ4bmRzTlc1T3A0NGNOMWFPZFp2a0kxRThzc1pkZFpFelN6allmMHZPVmRRYW9QckNPSkJiSlh2VUV3T1FXSU1sWnhFWFNYU2tWR1E4d2U5NXl5dnFvd1lWODJZT0pxTFZibUdwWUxQRnlzZm9yYzd4dnFWdDB1V3c2OE92NVRRWmdIL2RHR0U1MEg5ZXNyTmVIalRMSnJ5SjFsMkNQTGZGSVVERWJvUFYvWGlmNlBqa1MyeWZ3eFhqM05qRm9zWjV2bW9GQXMwc2h3U09oVThxdkpPTmgwdUtISFhQQldFcEIyR085bGw3aUJmczhBTVdSRzBtbkhmNGVaSVJ3akZtYTUgcm9vdEBrOHMtbWFzdGVyLW5vZGUxCg==
kind: Secret
metadata:
  name: my-pub-key
---
apiVersion: kubevirt.io/v1
kind: VirtualMachine
metadata:
  name: vm-fedora
  labels:
    vm: fedora
spec:
  #running: true
  runStrategy: Always
  template:
    metadata:
      labels:
        vm: fedora
    spec:
      domain:
        devices:
          disks:
          - name: containerdisk
            disk:
              bus: virtio
        resources:
          requests:
            memory: 1Gi
      volumes:
      - name: containerdisk
        containerDisk:
          image: fedora-virt:v1.0
      accessCredentials:
      - sshPublicKey:
          source:
            secret:
              secretName: my-pub-key
          propagationMethod:
            qemuGuestAgent:
              users:
              - fedora
```

# vmi创建
emptyDisk、cloudInitNoCloud

```yaml
apiVersion: kubevirt.io/v1
kind: VirtualMachineInstance
metadata:
  labels:
    special: fedora
  name: fedora
spec:
  domain:
    devices:
      disks:
        - disk:
            bus: virtio
          name: containerdisk
        - disk:
            bus: virtio
          name: cloudinitdisk
        - disk:
            bus: virtio
          name: emptydisk
      rng: {}
    resources:
      requests:
        memory: 1024M
  terminationGracePeriodSeconds: 0
  volumes:
    - containerDisk:
        image: fedora-virt:v1.0
        imagePullPolicy: IfNotPresent
      name: containerdisk
    - name: emptydisk
      emptyDisk:
        capacity: 2Gi
    - cloudInitNoCloud:
        userData: |-
          #cloud-config
          password: fedora
          chpasswd: { expire: False }
      name: cloudinitdisk
```
