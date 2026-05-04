# MiniKube 部署

MiniKube 是一个轻量级的 Kubernetes 本地集群工具，适合学习和开发测试使用。

## 安装

```bash
# Linux 安装
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# macOS 安装
brew install minikube
```

## 常用命令

```bash
# 启动集群（默认使用 Docker 驱动）
minikube start

# 指定 Kubernetes 版本
minikube start --kubernetes-version=v1.28.0

# 查看集群状态
minikube status

# 查看 Dashboard
minikube dashboard

# 停止集群
minikube stop

# 删除集群
minikube delete

# 查看集群节点
kubectl get nodes

# 查看集群信息
kubectl cluster-info
```

## 说明

| 参数 | 说明 |
| :--- | :--- |
| `--driver` | 指定驱动（docker, virtualbox, vmware） |
| `--nodes` | 创建多节点集群 |
| `--cpus` | 分配 CPU 核心数 |
| `--memory` | 分配内存大小 |
| `--disk-size` | 分配磁盘大小 |
