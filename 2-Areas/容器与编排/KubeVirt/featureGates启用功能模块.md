# FeatureGates 启用功能模块

FeatureGates 启用功能模块相关技术笔记。

KubeVirt 的 `featureGates` 字段用于启用不同的实验性功能，通过修改 KubeVirt CR 配置来开启。

```bash
kubectl explain kubevirt.spec.configuration.developerConfiguration.featureGates

#FIELD:    featureGates <[]string> 所需要的值为列表字符串形式
```

## 常见 FeatureGates 功能

| 功能名称 | 说明 |
| :--- | :--- |
| LiveMigration | 启用虚拟机的在线迁移功能，允许在物理主机之间无需中断地迁移运行中的虚拟机 |
| CpuModelEmulation | 启用 CPU 模型仿真功能，允许在虚拟机中使用特定的 CPU 模型 |
| Snapshot | 启用虚拟机的快照功能，允许创建和还原虚拟机状态的快照 |
| NodePortIPAM | 启用在 Kubernetes 集群中为虚拟机分配 NodePort 类型服务的 IP 地址管理功能 |
| AffinityInjection | 启用将亲和性注入到虚拟机调度过程中的功能 |
| DataVolumes | 启用 DataVolume 数据卷功能 |
