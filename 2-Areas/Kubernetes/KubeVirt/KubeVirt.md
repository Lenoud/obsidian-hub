# KubeVirt

KubeVirt相关技术笔记。

KubeVirt 是一个开源项目，它将 Kubernetes 转变成一个虚拟机管理平台。它通过将 QEMU/KVM 虚拟机和容器编排技术（如 Kubernetes）相结合，为 Kubernetes 用户提供了一种新的方法来运行虚拟机。

KubeVirt 提供了一个自定义资源类型（CRD），称为 Virtual Machine（VM），用于声明和管理虚拟机实例。它还包括一个自定义控制器，该控制器监听 VM 资源定义并确保为每个 VM 启动正确的 QEMU/KVM 虚拟机进程，并在需要时将其重新启动。此外，KubeVirt 还提供了一个基于 Web 的管理控制台，用于简化 VM 的创建和管理。

KubeVirt 主要解决以下问题:

+ Kubernetes 上的应用程序和虚拟机之间缺少无缝集成。
+ 缺乏一种统一的方式来管理和配置虚拟机。
+ 缺乏一种将虚拟化工作负载视为 Kubernetes 中的第一类公民的方法。

通过 KubeVirt，用户可以使用熟悉的 Kubernetes API 和命令行工具来管理虚拟机，从而使虚拟机成为 Kubernetes 中的第一类资源。这大大简化了虚拟机和容器之间的部署、管理和维护。

总的来说，KubeVirt 是一个有助于将虚拟机纳入 Kubernetes 集成和 DevOps 工作流的开源项目，它可以帮助用户在 Kubernetes 中管理虚拟化工作负载，并为用户提供了一种更加灵活、高效和可扩展的方式来管理这些工作负载。
