# 创建一个 VM 容器镜像

创建一个 VM 容器镜像相关技术笔记。

将 qcow2 格式的虚拟机磁盘镜像打包为 Docker/Containerd 容器镜像，供 KubeVirt 的 `containerDisk` 使用。

## Dockerfile

```dockerfile
FROM scratch
ADD cirros-0.5.2-x86_64-disk.img /disk/
```

## 构建命令

```bash
nerdctl -n k8s.io build -t cirros:v1.0 -f Dockerfile-cirros .
docker build  -t cirros:v1.0 .
```
