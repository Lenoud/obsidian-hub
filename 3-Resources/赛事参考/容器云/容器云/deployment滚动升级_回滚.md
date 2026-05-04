# Deployment 滚动升级与回滚

Kubernetes Deployment 的滚动更新策略和版本回滚操作。

## 滚动升级

```bash
# 更新镜像版本
kubectl set image deploy/nginx-app nginx=nginx:1.12.0 --record=true

# 或修改 YAML 后重新 apply
kubectl apply -f nginx-app.yaml
```

## 查看历史版本

```bash
kubectl rollout history deployment/nginx-app
```

## 回滚

```bash
# 回滚到指定版本
kubectl rollout undo deploy/nginx-app --to-revision=1
```

## 保留历史版本数

```yaml
spec:
  revisionHistoryLimit: 10  # 保留最近的 10 个 revision
```
