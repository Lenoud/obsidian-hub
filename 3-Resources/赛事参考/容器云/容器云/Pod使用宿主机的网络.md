# Pod 使用宿主机网络

通过设置 `hostNetwork: true` 让 Pod 直接使用宿主机的网络命名空间。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  hostNetwork: true
  containers:
  - name: my-container
    image: nginx
```

将 `hostNetwork` 设置为 `true`，Pod 中的容器将直接绑定到宿主机网络，共享宿主机的 IP 地址和端口。
