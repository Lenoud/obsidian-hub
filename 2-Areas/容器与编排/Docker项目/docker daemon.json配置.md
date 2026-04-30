# Docker daemon.json 配置

Docker 守护进程的配置文件，通常位于 `/etc/docker/daemon.json`。

## 配置项说明

| 参数 | 说明 |
|------|------|
| `registry-mirrors` | 配置镜像加速地址 |
| `insecure-registries` | 允许使用不安全的 IP 地址与 HTTP 协议进行镜像的拉取和推送 |
| `log-driver` | 配置日志驱动，默认为 `json-file` |

## 示例配置

```json
{
  "registry-mirrors": [
    "http://rockylinux:5000",
    "https://docker.1panel.live",
    "https://docker.1panelproxy.com",
    "https://proxy.1panel.live",
    "https://dockerproxy.1panel.live"
  ],
  "insecure-registries": ["0.0.0.0/0"],
  "log-driver": "syslog"
}
```
