# PriorityClass Pod 优先级抢占

使用 Kubernetes PriorityClass 定义 Pod 的调度优先级，高优先级 Pod 可以抢占低优先级 Pod 的资源。

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-scheduling
spec:
  value: 1000000
  globalDefault: false
```

| 参数 | 说明 |
|------|------|
| `value` | 优先级数值，越大优先级越高 |
| `globalDefault` | 是否设为默认优先级策略 |
