Kubernetes（k8s）是一个容器编排平台，其核心架构由多个组件协同工作，分为**控制平面（Control Plane）组件**和**节点（Node）组件**。以下是基本组件及其功能的总结：

---

### **一、控制平面组件（Control Plane）**
控制平面负责全局决策和集群状态管理，通常运行在独立的 Master 节点上。

#### 1. **kube-apiserver**
+ **作用**：集群的“中央枢纽”，暴露 Kubernetes API，处理所有 REST 操作（如创建、更新、删除资源）。
+ **关键点**：
    - 唯一直接与 `etcd` 通信的组件。
    - 支持认证、授权、准入控制（Admission Control）。

#### 2. **etcd**
+ **作用**：分布式键值存储数据库，保存集群的所有状态和配置数据。
+ **关键点**：
    - 集群的“唯一真实数据源”（Single Source of Truth）。
    - 需要高可用部署（如 3 节点或 5 节点集群）。

#### 3. **kube-controller-manager**
+ **作用**：运行一系列控制器（Controllers），确保集群实际状态与期望状态一致。
+ **核心控制器**：
    - **Node Controller**：监控节点状态。
    - **Deployment Controller**：管理 Deployment 的副本数。
    - **Service Controller**：处理 Service 与云平台负载均衡的交互。
    - **ReplicaSet Controller**：确保 Pod 副本数符合预期。

#### 4. **kube-scheduler**
+ **作用**：负责将新创建的 Pod 调度到合适的节点上。
+ **调度逻辑**：
    - 过滤不满足条件的节点（如资源不足）。
    - 对剩余节点打分（如负载均衡、亲和性规则）。

#### 5. **cloud-controller-manager**（可选）
+ **作用**：对接云服务商（如 AWS、Azure），管理云平台相关的资源（如负载均衡、存储卷）。
+ **核心控制器**：
    - Node Controller（云节点生命周期管理）。
    - Route Controller（配置云平台路由表）。

---

### **二、节点组件（Node Components）**
每个工作节点（Worker Node）上运行的组件，负责容器生命周期管理。

#### 1. **kubelet**
+ **作用**：节点上的“代理”，负责管理 Pod 的生命周期。
+ **功能**：
    - 从 kube-apiserver 接收 Pod 定义。
    - 启动、停止容器（通过 CRI，如 Docker、Containerd）。
    - 监控容器状态并上报给控制平面。

#### 2. **kube-proxy**
+ **作用**：维护节点上的网络规则（如 iptables/IPVS），实现 Service 的负载均衡和流量转发。
+ **核心功能**：
    - 将 Service 的虚拟 IP（ClusterIP）映射到后端 Pod。
    - 处理 NodePort、LoadBalancer 类型的 Service。

#### 3. **容器运行时（Container Runtime）**
+ **作用**：负责运行容器（如 Docker、Containerd、CRI-O）。
+ **接口**：通过 CRI（Container Runtime Interface）与 kubelet 交互。

---

### **三、附加组件（Addons）**
这些是可选的扩展组件，用于增强集群功能。

#### 1. **CoreDNS**
+ **作用**：为集群提供 DNS 服务，解析 Service 和 Pod 的域名。
+ **默认域名格式**：`<service-name>.<namespace>.svc.cluster.local`.

#### 2. **CNI 网络插件**
+ **作用**：实现 Pod 间网络通信（如 Calico、Flannel、Cilium）。
+ **核心功能**：
    - 分配 Pod IP。
    - 配置跨节点网络路由。

#### 3. **Ingress Controller**
+ **作用**：管理外部流量访问集群内部 Service（如 Nginx Ingress、Traefik）。
+ **功能**：基于 HTTP/HTTPS 路由规则转发请求。

#### 4. **Metrics Server**
+ **作用**：收集集群资源使用指标（如 CPU、内存），供 HPA（自动扩缩容）使用。

#### 5. **Dashboard**
+ **作用**：提供 Web UI 界面，可视化查看和管理集群资源。

---

### **四、核心概念与组件协作**
1. **用户操作流程**：
    - 用户通过 `kubectl` 或 API 提交资源定义（如 Deployment）。
    - `kube-apiserver` 接收请求并写入 `etcd`。
    - `kube-scheduler` 分配 Pod 到节点。
    - `kubelet` 在节点上启动容器。
    - `kube-proxy` 配置网络规则。
2. **关键协作**：
    - **控制器**持续监控资源状态，确保实际状态与声明一致。
    - **etcd** 作为数据存储，保证高可用和一致性。

---

### **总结表格**
| 组件 | 角色 | 关键功能 |
| --- | --- | --- |
| kube-apiserver | 中央 API 网关 | 处理 REST 请求，与 etcd 交互 |
| etcd | 分布式数据库 | 存储集群状态 |
| kube-controller-manager | 控制器集合 | 确保资源实际状态匹配期望状态 |
| kube-scheduler | 调度器 | 分配 Pod 到节点 |
| kubelet | 节点代理 | 管理 Pod 生命周期 |
| kube-proxy | 网络代理 | 维护 Service 网络规则 |
| CoreDNS | DNS 服务 | 解析集群内部域名 |
| CNI 插件 | 网络插件 | 实现 Pod 间通信 |

通过理解这些组件，可以更清晰地掌握 Kubernetes 的架构和运行机制。

## 相关笔记

- [[MOC-Kubernetes]]
- [[MOC-Docker]]
- [[kubectl运维命令]]
- [[pod运维]]
