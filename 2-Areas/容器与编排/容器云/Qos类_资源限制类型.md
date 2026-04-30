# QoS 类 — 资源限制类型

Kubernetes 根据 Pod 的资源请求和限制配置自动分配 QoS（Quality of Service）类别。

官方文档：[配置 Pod 的服务质量](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/)

## QoS 类别

| QoS 类 | 条件 | 说明 |
|--------|------|------|
| **Guaranteed** | limits == requests | 内存和 CPU 的限制等于请求 |
| **Burstable** | limits != requests | 限制和请求不一致 |
| **BestEffort** | 无限制和请求 | 未设置任何资源限制 |

## Guaranteed 示例

```yaml
resources:
  limits:
    memory: "200Mi"
    cpu: "500m"
  requests:
    memory: "200Mi"
    cpu: "500m"
```
