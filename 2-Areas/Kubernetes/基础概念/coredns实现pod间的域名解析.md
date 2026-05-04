# 添加dns解析
kubectl edit configmap coredns -n kube-system

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

```yaml
# 应用configmap到coredns
kubectl scale deployment coredns -n kube-system --replicas=0  #先缩容使pod为0
kubectl scale deployment coredns -n kube-system --replicas=2  #再扩容到原来的数量
```
