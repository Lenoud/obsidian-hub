# Deployment 运维

Deployment 运维相关技术笔记。

Kubernetes Deployment 的日常运维操作，包括 Service 暴露和系统镜像容器启动问题解决。

## 暴露 Deployment 为 Service

```bash
kubectl expose deployment wordpress --selector name=mysql --port=3306 \
--dry-run=client --type=NodePort --protocol tcp -oyaml
#注意生成后的模板还需要自己添加一条nodePort=xxx声明映射到主机的端口号
```

## 系统镜像容器无法启动的解决方法

K8s 使用 deploy 启动 ubuntu、centos 等系统镜像时，容器会自动退出。解决方法是在 yaml 中添加 `command` 让容器保持运行，并给予 root 权限：

```yaml
spec:
  containers:
    - name: ubuntu
      image: ubuntu
      command: ["/bin/bash", "-c", "--"]
      args: ["while true; do sleep 30; done;"]
      securityContext:
        privileged: true
```
