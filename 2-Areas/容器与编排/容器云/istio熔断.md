# Istio 熔断配置

使用 Istio DestinationRule 配置熔断器，限制并发连接和请求数，保护服务免受过载。

目标规则名称：httpbin，TLS 模式 ISTIO_MUTUAL，并发连接和请求数超过 1 时阻止后续请求。

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: httpbin
spec:
  host: httpbin
  trafficPolicy:
    tls:
      mode: ISTIO_MUTUAL
    connectionPool:
      http:
        http1MaxPendingRequests: 1
        maxRequestsPerConnection: 1
      tcp:
        maxConnections: 1
    outlierDetection:
      consecutiveErrors: 1
      interval: 1s
      baseEjectionTime: 3m
      maxEjectionPercent: 100
```
