# Istio 运维 — VirtualService 虚拟服务

使用 Istio VirtualService 管理服务网格中的流量路由规则。

将 Bookinfo 应用部署到 default 命名空间，为 reviews 服务创建 VirtualService，将 Jason 用户的流量路由到 v2 版本。

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews  #这里不能写"*"
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: reviews
        subset: v2
  - route:
    - destination:
        host: reviews
        subset: v3
```

# Sidecar管理
在prod-us1命名空间中声明一个名为default的Sidecar配置，允许向prod-us1、prod-apis和istio-system命名空间的公共服务输出流量。

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: prod-apis
---
apiVersion: networking.istio.io/v1alpha3
kind: Sidecar
metadata:
  name: exam
  namespace: prod-us1
spec:
  egress:
  - hosts:
    - "prod-us1/*"
    - "prod-apis/*"
    - "istio-system/*"
```
