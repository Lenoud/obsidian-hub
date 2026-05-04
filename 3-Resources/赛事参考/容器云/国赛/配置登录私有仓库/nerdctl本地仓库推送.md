# Nerdctl 本地仓库推送

Nerdctl 本地仓库推送相关技术笔记。

使用 nerdctl 配置 Containerd 的 Harbor 私有仓库地址并实现镜像推送。

## 配置 hosts.toml

```bash
mkdir -p /etc/containerd/certs.d/192.168.100.10
```

```toml
# vim hosts.toml
[host."http://192.168.100.105"]
  capabilities = ["pull", "resolve","push"]
```
