# 手动创建 Service Account 的 Token

手动创建 Service Account 的 Token相关技术笔记。

从 Kubernetes v1.24.0 开始，创建 ServiceAccount 不再自动生成 Secret，需要手动创建类型为 `kubernetes.io/service-account-token` 的 Secret。

## 手动创建 Secret

```yaml
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: devops-admin
  annotations:
    kubernetes.io/service-account.name: "devops-admin"
```

## 获取 Token

```bash
# 查看 Secret 详情
kubectl -n kube-system describe secrets admin-token
# 获取 Token
kubectl -n kube-system get secrets admin-token -oyaml
# 解码
echo "token" |base64 -d
```

## 使用 dry-run 生成模板

```bash
kubectl create secret generic kubernetes-dashboard --namespace=default \
--type=kubernetes.io/service-account-token --dry-run=client -o yaml
```

生成后添加 annotations：

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: kubernetes-dashboard
  namespace: default
type: kubernetes.io/service-account-token
  annotations:
    kubernetes.io/service-account.name: "devops-admin"
```

## 参考

ServiceAccounts 及 Secrets 重大变化：[https://blog.csdn.net/qq_33921750/article/details/124977220](https://blog.csdn.net/qq_33921750/article/details/124977220)
