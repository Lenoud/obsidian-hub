# kubectl 运维命令

kubectl 常用运维命令速查。

## 资源字段查询

```bash
# 逐级查询资源定义需要什么字段
kubectl explain pod.spec.containers.ports
```

## dry-run 试运行

```bash
# 试运行，不实际创建资源，用于生成 YAML 模板
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml
```
