# kubectl运维命令

kubectl 是 Kubernetes 集群的命令行管理工具，通过 API Server 与集群交互，用于部署应用、查看资源、排查故障、运维管理。

## 基本语法

```bash
kubectl [command] [TYPE] [NAME] [flags]
```

| 组成 | 说明 | 示例 |
| --- | --- | --- |
| command | 子命令 | `get` `describe` `apply` `delete` `exec` |
| TYPE | 资源类型 | `pod` `deploy` `svc` `ns`（支持简写） |
| NAME | 资源名称 | `nginx-xxx` |
| flags | 选项 | `-n app` `-o wide` `--watch` |

常用资源类型简写对照：

| 完整名 | 简写 | 完整名 | 简写 |
| --- | --- | --- | --- |
| pods | po | services | svc |
| deployments | deploy | replicasets | rs |
| namespaces | ns | nodes | no |
| configmaps | cm | secrets | secret |
| persistentvolumes | pv | persistentvolumeclaims | pvc |
| statefulsets | sts | daemonsets | ds |
| ingresses | ing | jobs | job |

## 通用 flags

| flag | 作用 |
| --- | --- |
| `-n, --namespace` | 指定命名空间 |
| `-A, --all-namespaces` | 所有命名空间 |
| `-o wide` | 宽格式输出（含 IP、Node） |
| `-o yaml` / `-o json` | YAML / JSON 格式输出 |
| `-o name` | 仅输出资源名 |
| `-w, --watch` | 持续监听变化 |
| `--show-labels` | 显示标签 |
| `-l app=nginx` | 按标签筛选 |
| `--dry-run=client -o yaml` | 试运行，生成清单不提交 |
| `--sort-by=.metadata.creationTimestamp` | 按字段排序 |

## 资源对象速查

### Node / Namespace

```bash
kubectl get nodes -o wide
kubectl describe node node01
kubectl cordon node01          # 标记为不可调度
kubectl drain node01 --ignore-daemonsets --delete-emptydir-data   # 驱逐工作负载
kubectl uncordon node01        # 恢复调度

kubectl get ns
kubectl create ns app
kubectl delete ns app          # 注意：会删除该 ns 下所有资源
```

### Pod

```bash
kubectl get pods -A -o wide
kubectl get pods -n app -l app=nginx
kubectl describe pod nginx-xxx -n app

kubectl logs nginx-xxx -n app                  # 查看日志
kubectl logs -f nginx-xxx -c sidecar           # 多容器时指定容器，-f 跟踪
kubectl logs --previous nginx-xxx              # 查看上一次崩溃的日志

kubectl exec -it nginx-xxx -- /bin/sh          # 进入容器
kubectl exec -n app nginx-xxx -- ls /etc/nginx

kubectl port-forward svc/nginx 8080:80         # 本地端口转发，访问 svc
kubectl cp app/nginx-xxx:/etc/hosts ./hosts    # 拷贝文件
kubectl delete pod nginx-xxx                   # 删除（Deployment 会重建）
```

### Deployment（扩缩容 / 滚动 / 回滚）

```bash
kubectl get deploy -A
kubectl describe deploy nginx -n app

kubectl scale deploy nginx -n app --replicas=5
kubectl autoscale deploy nginx -n app --min=2 --max=10 --cpu-percent=70   # HPA

kubectl rollout status deploy/nginx -n app
kubectl rollout history deploy/nginx -n app
kubectl rollout history deploy/nginx -n app --revision=3
kubectl rollout undo deploy/nginx -n app                       # 回滚到上一版本
kubectl rollout undo deploy/nginx -n app --to-revision=2
kubectl rollout restart deploy/nginx -n app                    # 重启 Pod
```

### Service / Endpoints

```bash
kubectl get svc -A -o wide
kubectl describe svc nginx -n app
kubectl get endpoints nginx -n app

# 临时暴露 Deployment 为 Service
kubectl expose deploy nginx --port=80 --target-port=80 --type=NodePort -n app
```

### ConfigMap / Secret

```bash
kubectl get cm -A
kubectl describe cm nginx-conf -n app
kubectl create cm app-conf --from-file=config.ini --from-literal=DEBUG=true -n app

kubectl get secret -A
kubectl describe secret db-secret -n app
kubectl get secret db-secret -o jsonpath='{.data.password}' | base64 -d ; echo
kubectl create secret generic db-secret \
    --from-literal=user=admin --from-literal=password=StrongPwd -n app
```

### PV / PVC

```bash
kubectl get pv                                  # 集群级存储
kubectl get pvc -A                              # 命名空间级
kubectl describe pvc data-pvc -n app
kubectl get sc                                  # StorageClass
```

### 标签 / 选择器 / 注解

```bash
kubectl get pods --show-labels -n app
kubectl label pod nginx-xxx env=prod -n app     # 添加
kubectl label pod nginx-xxx env- -n app         # 删除（key 后加 -）
kubectl get pods -l 'app in (nginx,api),env=prod' -n app
kubectl annotate pod nginx-foo owner=team-a -n app
```

## 常用排障组合

```bash
# 集群整体健康
kubectl get componentstatuses
kubectl get events -A --sort-by=.lastTimestamp

# 资源清单导出（备份 / 迁移）
kubectl get deploy nginx -n app -o yaml --export > nginx.yaml

# explain：查看资源字段定义（逐级查）
kubectl explain pod.spec.containers.ports
kubectl explain deploy.spec.strategy.rollingUpdate

# dry-run：生成清单不真正提交
kubectl run nginx --image=nginx:1.25 --dry-run=client -o yaml > pod.yaml
```

## 相关笔记

- [[pod运维]]
- [[deployment运维]]
- [[网络服务svc]]
- [[MOC-Kubernetes]]
