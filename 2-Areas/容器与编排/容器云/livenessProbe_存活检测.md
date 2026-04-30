# LivenessProbe 存活检测

使用 Kubernetes livenessProbe 对容器进行存活检测，当检测失败时自动重启容器。

## Exec 探针示例

Pod 名称：liveness-exec，使用 busybox 镜像，通过 `cat /tmp/healthy` 命令进行存活探测，每 5 秒执行一次。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: podlive
  namespace: default
spec:
  containers:
  - name: liveness-exec-container
    image: busybox
    command: ["/bin/sh","-c","touch /tmp/healthy;sleep 30; rm -rf /tmp/healthy"]
    livenessProbe:
      exec:
        command: ["cat","/tmp/healthy"]
      initialDelaySeconds: 1
      periodSeconds: 5
```

## 参数说明

| 参数 | 说明 |
|------|------|
| `exec.command` | 在容器内执行的探测命令 |
| `initialDelaySeconds` | 容器启动后延迟多久开始探测 |
| `periodSeconds` | 探测间隔时间（秒） |

容器启动后创建 `/tmp/healthy` 文件，30 秒后删除。探针每 5 秒检查文件是否存在，检测失败则重启容器。
