# LimitRange 容器资源限制

使用 Kubernetes LimitRange 为命名空间中的容器设置默认的 CPU 和内存请求与限制。

## CPU 限制示例

命名空间 default-cpu-example，默认 CPU 请求 500m，限制 2000m。

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-limit-range
  namespace: default-cpu-example
spec:
  limits:
  - default:
      cpu: 2000m
    defaultRequest:
      cpu: 500m
    type: Container
```

## CPU + 内存综合限制

LimitRange 名称：cpu-resource-constraint，限定 CPU 和内存的默认值、最大值和比值。

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-resource-constraint
  namespace: cpu-ram-resourcequota
spec:
  limits:
  - default:        # 默认限制值（Pod 未声明限制时使用）
      cpu: 500m
      memory: 256Mi
    defaultRequest:  # 默认请求值
      cpu: 500m
      memory: 256Mi
    max:             # 限制范围上限
      cpu: "3"
      memory: 800Mi
    min:             # 限制范围下限
      cpu: 300m
      memory: 100Mi
    type: Container
    maxLimitRequestRatio:
      cpu: 2         # CPU limit/request 比值最大为 2
      memory: 2      # 内存 limit/request 比值最大为 2
```
