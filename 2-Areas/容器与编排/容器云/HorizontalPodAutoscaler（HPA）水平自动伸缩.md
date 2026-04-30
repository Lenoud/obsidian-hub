# HPA 水平自动伸缩

使用 Kubernetes HorizontalPodAutoscaler 根据 CPU 使用率自动调整 Pod 副本数量。

HPA 名称：scale，最小副本数 1，最大副本数 5，目标 CPU 使用率 75%。

```bash
kubectl autoscale deployment scale \
  --cpu-percent=75 --min=1 --max=5 --dry-run=client -o yaml
```
