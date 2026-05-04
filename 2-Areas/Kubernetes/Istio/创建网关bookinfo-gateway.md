# 创建网关 bookinfo-gateway

创建网关 bookinfo-gateway相关技术笔记。

为 Bookinfo 应用创建 Istio Gateway 网关，指定所有 HTTP 流量通过 80 端口流入网格。

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: bookinfo-gateway
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
     number: 80
     name: http
     protocol: HTTP
    hosts:
    - "*"
```
