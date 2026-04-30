# HPA 管理 — 创建 HPA 规则

在 Kubernetes 集群中创建 HorizontalPodAutoscaler，自定义扩容灵敏度和伸缩范围。

在 default 命名空间下使用 nginx 镜像创建名为 web 的 Deployment，并创建同名 HPA。扩容时立即新增当前 9 倍数量的副本数，时间窗口为 5s，伸缩范围为 1–1000。

例如一开始只有 1 个 Pod，当 CPU 使用率超过 80% 时，Pod 数量变化趋势为：1 → 10 → 100 → 1000。

## HPA 配置

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web
spec:
  minReplicas: 1
  maxReplicas: 1000
  metrics:
  - pods:
      metric:
        name: k8s_pod
      target:
        averageValue: "80"
        type: AverageValue
    type: Pods
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web
  behavior:
    scaleUp:
      policies:
      - periodSeconds: 5
        value: 900
        type: Percent
```
