# pod运维

pod运维相关技术笔记。

Kubernetes 容器编排相关笔记。

```bash
kubectl exec podname  -- printenv
```

```bash
kubectl exec -it  PodName  -c [复数应用需要指定名称] bash
```

```bash
kubectl run wordpress --image=wordpress --image-pull-policy=Never --port=80 \
--labels=app=nginx,server=web --env PASSWORD=000000 --env USERNAME=liubiao \
--dry-run=client -oyaml
#只拉取本地的镜像，暴露80端口，打上两个标签，定义了环境变量
```

```bash
kubectl describe pod PodName
```

```bash
kubectl logs PodName AppName
```

```yaml
kubectl explain pod #or deployment sc rc svc ------等
```
