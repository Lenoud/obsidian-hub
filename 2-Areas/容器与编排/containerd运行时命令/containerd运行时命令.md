# Containerd 运行时命令

Containerd 运行时命令相关技术笔记。

介绍 `crictl` 和 `ctr` 两个 Containerd 容器运行时命令行工具的区别与用途。

## 说明

| 工具 | 全称 | 用途 | 版本输出 |
| :--- | :--- | :--- | :--- |
| crictl | CRI CLI | 检查和管理 kubelet 节点上的容器运行时和镜像 | 输出 k8s 版本 |
| ctr | Containerd CLI | Containerd 的原生客户端工具 | 输出 containerd 版本 |

## 工具对比

- **crictl** 是遵循 CRI 接口规范的一个命令行工具，通常用它来检查和管理 kubelet 节点上的容器运行时和镜像。
- **ctr** 是 containerd 的一个客户端工具。
- `ctr -v` 输出的是 containerd 的版本，`crictl -v` 输出的是当前 k8s 的版本，从结果显而易见你可以认为 crictl 是用于 k8s 的。
