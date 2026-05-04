# 资源限制 QoS Class 三种类型

资源限制 QoS Class 三种类型相关技术笔记。

Kubernetes 根据 Pod 的资源请求（requests）和限制（limits）配置自动划分为三种 QoS 类别：Guaranteed、Burstable 和 BestEffort。

## 说明

| QoS 类型 | 条件 | 优先级 |
| :--- | :--- | :--- |
| Guaranteed（保证类） | requests 和 limits 完全相同 | 最高 |
| Burstable（可突发类） | requests 小于 limits | 中等 |
| BestEffort（尽力而为类） | 不定义 resources | 最低 |

## Guaranteed（保证类）

请求和限制都是相同的：

```yaml
resources:
  requests:
    memory: "256Mi"
    cpu: "500m"
  limits:
    memory: "256Mi"
    cpu: "500m"
```

## Burstable（可突发类）

请求小于限制：

```yaml
resources:
  requests:
    memory: "128Mi"
    cpu: "100m"
  limits:
    memory: "256Mi"
    cpu: "500m"
```

## BestEffort（尽力而为类）

不定义 resources：

```yaml
resources: {}
```

> QoS 类别是根据资源请求和使用情况自动确定的，无法手动设置。可通过 `kubectl describe pods` 查看 Pod 的 QoS Class。
