# 启动一个 OpenWrt 虚拟机

启动一个 OpenWrt 虚拟机相关技术笔记。

使用 KubeVirt 的 `containerDisk` 方式启动 OpenWrt 虚拟机，支持以 VM（VirtualMachine）和 VMI（VirtualMachineInstance）两种形式部署，并通过 NodePort 暴露 Web 和 SSH 端口。

volumes.containerDisk：指定一个包含 qcow2 或 raw 格式的 Docker 镜像，重启 VM 数据会
丢失。

# 1.以vm的形式启动
```yaml
#Dockerfile-openwrt 制作启动镜像容器
FROM scratch
ADD images/openwrt-22.03.5-x86-64.qcow2 /disk/

#以vm形式启动虚拟机并且绑定nodeport暴露端口服务
apiVersion: kubevirt.io/v1
kind: VirtualMachine
metadata:
  name: vm-openwrt
  labels:
    kubevirt.io/vm: vm-openwrt
spec:
  running: false
  template:
    metadata:
      labels:
        kubevirt.io/vm: vm-openwrt
    spec:
      domain:
        resources:
          requests:
            memory: 1Gi
        devices:  #设备允许添加磁盘、网络接口和其他
          disks:  #磁盘描述连接到vmi
          - name: containerdisk
            disk:   #将卷作为磁盘连接到vmi
              bus: virtio  #总线指示要仿真的磁盘设备的类型,支持的值:virtio,sata,scsi
      volumes:  #属于vmi的磁盘可以装载的卷列表
      - name: containerdisk
        containerDisk:
          image: vm-openwrt:v1.0
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: vm-openwrt
  name: vm-openwrt
spec:
  ports:
  - name: web
    nodePort: 38888
    port: 80
    protocol: TCP
    targetPort: 80

  - name: ssh
    nodePort: 38822
    port: 22
    protocol: TCP
    targetPort: 22

  selector:
    kubevirt.io: virt-launcher
    kubevirt.io/vm: vm-openwrt
    vm.kubevirt.io/name: vm-openwrt
  type: NodePort

```

# 2.以vmi的形式启动
```yaml
apiVersion: kubevirt.io/v1
kind: VirtualMachineInstance
metadata:
  labels:
    vm: vmi-openwrt  #打上标签
  name: vmi-openwrt
spec:
  domain:
    devices:
      disks:
      - disk:
          bus: virtio
        name: containerdisk
    resources:
      requests:
        memory: 1024M
  volumes:
  - containerDisk:
      image: vm-openwrt:v1.0
      imagePullPolicy: IfNotPresent
    name: containerdisk
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: vmi-openwrt
  name: vmi-openwrt
spec:
  ports:
  - name: web
    nodePort: 38888
    port: 80
    protocol: TCP
    targetPort: 80

  - name: ssh
    nodePort: 38822
    port: 22
    protocol: TCP
    targetPort: 22

  selector:
    kubevirt.io: virt-launcher
    vm: vmi-openwrt
    vm.kubevirt.io/name: vmi-openwrt
  type: NodePort

```
