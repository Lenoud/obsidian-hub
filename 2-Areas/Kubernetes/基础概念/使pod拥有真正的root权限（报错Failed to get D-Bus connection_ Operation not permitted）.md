# 使pod拥有真正的root权限（报错Failed to get D-Bus connection_ Operation not permitted）

使pod拥有真正的root权限（报错Failed to get D-Bus connection_ Operation not permitted）相关技术笔记。

Kubernetes 容器编排相关笔记。

k8s：启动pod的yaml中添加参数：注意yaml格式的对齐

```yaml
command: ["/usr/sbin/init", "-c","--"]   #不确定需不需要加这个
securityContext:
 privileged: true
```

1. 添加参数之后，container内的root拥有真正的root权限

2. 权限参数命令在init文件中

具体yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: text2
  namespace: text
spec:
  containers:
  - name: text2
    image: centos:7
    command: ["/usr/sbin/init", "-c","--"]
    securityContext:
      privileged: true
```
