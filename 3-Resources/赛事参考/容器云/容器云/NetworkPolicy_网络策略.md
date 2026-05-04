# NetworkPolicy 网络策略

使用 Kubernetes NetworkPolicy 控制 Pod 之间的网络访问，限制入站和出站流量。

## 同命名空间访问策略

策略名称：exam-nework，仅允许同命名空间下的 Pod 访问 9000 端口。

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: exam-nework
  namespace: test
spec:
  podSelector: {}
  policyTypes:
    - Ingress
  ingress:
    - from:
      - podSelector: {}
        namespaceSelector:
          matchLabels:
            name: test
      ports:
        - protocol: TCP
          port: 9000
```

## 多命名空间访问策略

允许同命名空间、默认命名空间和 git 命名空间访问 80/443 端口。

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-by-default
  namespace: exam
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector: {}
      namespaceSelector:
        matchLabels:
          name: "exam"
    - podSelector: {}
      namespaceSelector: null      # 默认命名空间
    - podSelector: {}
      namespaceSelector:
        matchLabels:
          name: "git"
    ports:
    - protocol: TCP
      port: 80
    - protocol: TCP
      port: 443
```
