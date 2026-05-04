# MOC - 知识地图

技术知识库导航入口，按 PARA 层级组织。所有技术领域通过 MOC 页面串联，跨领域主题通过底部索引横向关联。

## 容器与编排
- [[MOC-Docker]] — Docker 容器技术、Compose 编排、私有仓库、数据库容器化部署
- [[MOC-Kubernetes]] — K8s 集群搭建、服务部署、Istio 服务网格、KubeVirt 虚拟化
- [[MOC-CICD]] — Jenkins / Drone / GitLab-CI 持续集成与持续部署

## 基础设施运维
- [[MOC-Linux运维]] — 操作系统管理、存储配置、防火墙、虚拟化
- [[MOC-网络技术]] — Cisco 配置、软路由、NAT 内网穿透、路由交换
- [[MOC-自动化运维]] — Ansible 配置管理、Shell 脚本编程

## 数据与应用
- [[MOC-数据库]] — MySQL / PostgreSQL / Redis / MongoDB / SQL Server 部署与运维
- [[MOC-服务部署]] — Nginx / Prometheus / RabbitMQ / Clash 等服务搭建
- [[MOC-开发编程]] — Git / API / Python / Golang / Java
- [[MOC-Windows]] — Windows 使用技巧、bat 脚本、Office

## 参考资料
- [[MOC-面试题]] — 按 Linux / 容器 / 数据库 / 网络分类的面试准备资料

## 跨领域主题索引

### 容器化部署
同一技术在 Docker 和 K8s 两种环境下的部署方式：
- 数据库 → [[docker部署mysql]] / [[docker部署redis]] / [[docker部署postgresql]] / [[docker部署mongodb主从哨兵]]
- 监控 → [[Prometheus-monitor]] / [[Prometheus-k8s]] / [[docker_exporter]] / [[mysqld_exporter]]
- CICD → [[jenkins for docker]] / [[docker+gogs+drone]]
- 服务 → [[docker部署nginx]] / [[Docker容器部署Rabbitmq HA集群]] / [[docker部署wordpress]]

### Prometheus 监控体系
跨越 Docker 和 K8s 两个部署环境的完整监控栈：
- Docker 部署 → [[Prometheus-monitor]]
- K8s 部署 → [[Prometheus-k8s]]
- 系统监控 → [[promethues系统监控]]
- 专用 Exporter → [[mysqld_exporter]] / [[docker_exporter]]

### 网络与穿透
- [[zerotier]] — Docker 部署与网络配置
- [[内网穿透]] — NPS / ZeroTier 等方案
- [[v2ray代理服务器部署]] — 代理服务

### 自动化部署
- [[offline install docker]] — Shell 脚本离线安装 Docker
- [[nfs服务部署]] — Ansible 自动化 NFS 部署
- [[初始化centos7脚本]] / [[初始化ubuntu2204脚本]] — 系统初始化
