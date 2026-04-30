# Pod 运维

Kubernetes Pod 的日常运维命令集合，包括环境变量查看、终端进入、Pod 创建、状态查看和日志查看等操作。

## 常用运维命令

```bash
# 查看环境变量
kubectl exec podname -- printenv

# 进入容器终端（多容器时用 -c 指定容器名）
kubectl exec -it PodName -c [容器名] bash

# 创建 Pod（使用本地镜像、打标签、设置环境变量）
kubectl run wordpress --image=wordpress --image-pull-policy=Never --port=80 \
  --labels=app=nginx,server=web --env PASSWORD=000000 --env USERNAME=liubiao \
  --dry-run=client -oyaml

# 查看 Pod 详情和事件
kubectl describe pod PodName

# 查看 Pod 日志（多容器时指定 AppName）
kubectl logs PodName AppName

# 查询资源字段定义
kubectl explain pod
```
