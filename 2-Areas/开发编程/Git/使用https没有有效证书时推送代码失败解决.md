# HTTPS 推送代码 SSL 证书验证失败解决

当使用 HTTPS 方式推送代码到自建 Git 服务器时，因没有有效 SSL 证书导致推送失败。

## 解决方法

禁用 Git 的 SSL 验证（会增加安全风险，仅用于内网自建服务）：

```bash
git config --global http.sslVerify false
```
