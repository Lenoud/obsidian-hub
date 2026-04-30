# Helm 配置源

通过 Helm v3 客户端添加 Chart 仓库并拉取应用模板。

参考文档：[通过 Helm v3 客户端部署应用 - 华为云](https://support.huaweicloud.com/usermanual-cce/cce_10_0144.html)

从 [Artifact Hub](https://artifacthub.io/packages/search?kind=0) 查找有效的 Helm Chart 仓库。

## 常用命令

```bash
# 添加 Bitnami 仓库
helm repo add bitnami https://charts.bitnami.com/bitnami

# 搜索 Chart
helm search repo bitnami/wordpress

# 拉取指定版本
helm pull bitnami/wordpress --version 13.0.23
helm pull bitnami/wordpress --version 18.1.17
```
