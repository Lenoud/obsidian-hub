# 创建 Secret

创建 Secret相关技术笔记。

使用 `kubectl create secret` 命令通过 `--dry-run` 快速生成 Secret 的 YAML 资源清单。

## 从文件创建 Secret

```bash
kubectl create secret -n logging generic elastic-certs --from-file=elastic-certificates.p12 --dry-run -oyaml
```

## 从字面量创建 Secret

```bash
kubectl create secret -n logging generic elastic-auth --from-literal "username=elastic" --from-literal "password=admin123"  --dry-run -oyaml
```
