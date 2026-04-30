# 使 Pod 拥有真正的 root 权限

使 Pod 拥有真正的 root 权限相关技术笔记。

解决 K8s 中启动 centos/ubuntu 等系统镜像时报错 `Failed to get D-Bus connection: Operation not permitted` 的问题，通过添加 `privileged: true` 和 init 命令赋予容器真正的 root 权限。

## 解决方法

在 Pod 的 YAML 中添加参数（注意 YAML 格式的对齐）：

```yaml
command: ["/usr/sbin/init", "-c","--"]
securityContext:
  privileged: true
```

1. 添加参数之后，container 内的 root 拥有真正的 root 权限
2. 权限参数命令在 init 文件中

## 完整示例

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
