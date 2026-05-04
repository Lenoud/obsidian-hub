# Role 和 RoleBinding 的创建

Role 和 RoleBinding 的创建相关技术笔记。

使用 `kubectl create` 命令快速创建 Role、RoleBinding、ClusterRole 和 ClusterRoleBinding，并解释 SA 与角色绑定的权限范围关系。

## 常用创建命令

```bash
kubectl create role gitlab-ci --namespace gitlab-ci --verb="*" --resource="*" --dry-run=client -oyaml >> runner-rbac.yaml
kubectl create rolebinding gitlab-ci --role=gitlab-ci --serviceaccount=gitlab-ci:gitlab-ci  --namespace=gitlab-ci  --dry-run -oyaml

---

kubectl create clusterrole manage-pods \
    --verb=get --verb=list --verb=watch --verb=create --verb=update --verb=patch --verb=delete \
    --resource=pods

kubectl -n default create rolebinding sa-manage-pods \
    --clusterrole=manage-pods \
    --serviceaccount=default:playground

kubectl create clusterrolebinding sa-cluster-admin \
  --clusterrole=cluster-admin \
  --serviceaccount=default:playground
```

## SA 与角色绑定的权限范围

| 绑定方式 | 权限范围 |
| :--- | :--- |
| SA + RoleBinding + Role | 具有 Role 权限，只能操作 Role 所在 namespace |
| SA + RoleBinding + ClusterRole | 具有 ClusterRole 权限，只能操作 SA 所在的 namespace |
| SA + ClusterRoleBinding + ClusterRole | 具有 ClusterRole 权限，可操作所有 namespace 和集群资源 |

> 没有 ClusterRoleBinding 与 Role 的组合。

> SA 通过 RoleBinding 绑定 Role 可以实现 SA 具有操作别的 namespace 中资源的权限（例如 SA 在 ns1, Role 在 ns2，SA 可操作 ns2 资源）。

原文链接：[https://blog.csdn.net/qq_24433609/article/details/127391726](https://blog.csdn.net/qq_24433609/article/details/127391726)
