# Helm 操作速查表

Kubernetes Helm 包管理工具的常用命令参考。

## 常用命令

| 命令 | 说明 |
| --- | --- |
| `helm version` | 查看版本信息 |
| `helm list` | 列出已安装的 releases |
| `helm install <release-name> <chart>` | 安装一个 chart |
| `helm uninstall <release-name>` | 卸载一个 release |
| `helm upgrade <release-name> <chart>` | 更新已安装的 chart |
| `helm rollback <release-name> <revision>` | 回滚到之前的版本 |
| `helm history <release-name>` | 查看 release 的历史记录 |
| `helm show chart <chart>` | 查看 chart 的详细信息 |
| `helm show values <chart>` | 查看 chart 默认的 values.yaml |
| `helm status <release-name>` | 查看 release 的详细信息 |
| `helm search repo <keyword>` | 搜索可用的 charts |
| `helm repo add <repo-name> <repo-url>` | 添加一个 chart 仓库 |
| `helm repo update` | 更新 chart 仓库 |
| `helm lint <chart>` | 检查 chart 是否符合要求 |
| `helm package <chart>` | 打包一个 chart |
| `helm create <chart-name>` | 创建一个新 chart |
| `helm template <chart-package>.tgz` | 查看生成的 yaml 文件 |
| `helm delete --purge <chart-name>` | 删除 chart |
