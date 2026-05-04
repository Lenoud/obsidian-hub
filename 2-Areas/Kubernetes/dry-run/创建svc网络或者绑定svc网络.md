# 创建 Service 或绑定 Service

创建 Service 或绑定 Service相关技术笔记。

使用 `kubectl expose` 和 `kubectl create svc` 命令通过 `--dry-run` 快速生成 Service 的 YAML 资源清单。

## 通过 expose 绑定 Deployment

```bash
kubectl expose deployment gitlab --type=NodePort --port=80 --target-port=80 --name=gitlab  --dry-run -oyaml
#绑定deploy
```

## 创建 NodePort 类型的 Service

```bash
kubectl create svc nodeport  gitlab --tcp 80:80 --node-port 30888  --dry-run  -oyaml
#创建nodeport的svc默认绑定 app=SvcName 标签
```
