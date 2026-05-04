# Secret 加密配置

使用 Kubernetes Secret 存储敏感信息（如密码、令牌），数据以 Base64 编码存储。

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: cGFzc3dvcmQxMjM=
  # "password123" 的 Base64 编码：echo -n "password123" | base64
```
