# CoreDNS 实现 Pod 间的域名解析

CoreDNS 实现 Pod 间的域名解析相关技术笔记。

通过修改 CoreDNS 的 ConfigMap 添加自定义 hosts 解析记录，实现 Pod 之间通过域名互相访问。

## 修改 CoreDNS 配置

```bash
kubectl edit configmap coredns -n kube-system
```

```yaml
        ready
        kubernetes cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           fallthrough in-addr.arpa ip6.arpa
           ttl 30
        } #加入下面的内容
         hosts {
            192.168.100.40 apiserver.cluster.local
            10.244.1.47 gitlab-c9d9756fb-p684d
            fallthrough
        }
```

## 应用 ConfigMap 到 CoreDNS

```bash
kubectl scale deployment coredns -n kube-system --replicas=0  #先缩容使pod为0
kubectl scale deployment coredns -n kube-system --replicas=2  #再扩容到原来的数量
```
