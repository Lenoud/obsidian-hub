# HostAliases 向 hosts 文件添加条目

使用 Kubernetes HostAliases 为 Pod 的 /etc/hosts 文件添加自定义解析条目。

Pod 名称：hostaliases-pod，将 foo.local、bar.local 解析为 127.0.0.1，将 foo.remote、bar.remote 解析为 10.1.2.3。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostaliases-pod
spec:
  containers:
  - name: hostaliases-container
    image: busybox
    command:
      - "/bin/sh"
      - "-c"
      - "sleep 3600"
  hostAliases:
  - ip: "127.0.0.1"
    hostnames:
    - "foo.local"
    - "bar.local"
  - ip: "10.1.2.3"
    hostnames:
    - "foo.remote"
    - "bar.remote"
```

验证：

```bash
kubectl exec -it hostaliases-pod -- cat /etc/hosts
```
