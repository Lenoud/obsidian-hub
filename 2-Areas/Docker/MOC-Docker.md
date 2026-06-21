# MOC - Docker

Docker 容器技术相关的学习笔记与实战记录，涵盖基础概念、Compose 编排、私有仓库、Containerd 运行时、数据库容器化部署以及各类项目实践。

## Containerd
- [[containerd运行时命令]]
- [[镜像导入]]
- [[配置登录私有仓库]]
- [[推送镜像脚本]]

## Docker-Compose
- [[Docker-compose部署]]
- [[wordpress]]

## Docker基础
- [[docker daemon.json配置]]
- [[dockerfile]]
- [[Docker搭建]]
- [[docker基础运维命令]]
- [[Docker命令自动补全]]
- [[Docker网络详解]]
- [[install docker-ce]]
- [[搭建harbor私有仓库]]
- [[基于cadvisor监控docker容器]]
- [[基于WeaveScope工具管理docker容器]]

## Docker教材
- [[docker容器目录20241014]]
- [[第一章-修改意见V2]]
- [[项目二 Docker镜像容器与仓库管理]]
- [[项目一 Docker基础认知与安装 副本]]
- [[项目一 Docker基础认知与安装]]

## Registry私有仓库
- [[搭建Reistry私有仓库]]
- [[配置Docker Registry管理界面]]

## 数据库

> 职责:本目录仅收录**使用 Bitnami 镜像、独立版本(如含 my.cnf)、集群形态、或与消息队列/数据可视化相关的容器化部署**。
> 基础数据库 Docker 部署(Redis 单机、MariaDB、PostgreSQL、SQL Server、MySQL 5.7+8 对比版)及数据库本身运维,见 [[MOC-数据库]]。

### 数据库引擎
- [[mysql57]] — 含完整 `my.cnf` 配置说明
- [[mysql8]] — 含完整 `my.cnf` 配置说明
- [[mongodb(bitnami)]] — Bitnami 镜像 + 环境变量详表
- [[redis(bitnami)]] — 主从复制架构
- [[rabbitmq]] — 三节点集群部署

### 数据可视化与管理
- [[dataease]] — 开源 BI 平台

### 数据库基础部署与运维
→ 见 [[MOC-数据库]],涵盖 adminer / dbgate / phpmyadmin 等管理工具及各数据库的部署、权限、备份、SQL 语法

## 项目
- [[AI_ollama]]
- [[alist]]
- [[Docker Registry]]
- [[docker-compose Managr]]
- [[DockerComposeManager]]
- [[docker部署wordpress]]
- [[docker启动一个火狐浏览器]]
- [[firefox]]
- [[Gitea(代码仓库)]]
- [[homebox内网测速]]
- [[ITtools]]
- [[jenkins]]
- [[jsontool]]
- [[logstash(bitnami)]]
- [[nginx]]
- [[owncloud网盘]]
- [[Portainer]]
- [[Prometheus-monitor]]
- [[tomcat8(bitnami)]]
- [[voicevox]]
- [[wordpress]]
- [[zerotier]]
- [[调试网络的容器]]
- [[容器轻量级可视化工具Portainer]]

## 游戏
- [[demo-2048]]
- [[MC服务器]]

## 相关领域
- [[MOC-Kubernetes]] — Docker 容器编排平台
- [[MOC-CICD]] — Jenkins / Drone 等基于 Docker 的 CICD 工具
- [[MOC-数据库]] — 数据库容器化部署的运维知识
- [[MOC-服务部署]] — Prometheus / Nginx 等服务的 Docker 部署
- [[MOC-网络技术]] — ZeroTier / 内网穿透等 Docker 网络相关
