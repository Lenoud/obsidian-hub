目录

[一、设计背景](#_Toc147820617)

[1.1项目简介](#_Toc147820618)

[1.2课题目标](#_Toc147820619)

[二、设计思路](#_Toc147820620)

[2.1 开发环境与工具](#_Toc147820621)

[2.2 系统版本](#_Toc147820622)

[2.3 系统设计](#_Toc147820623)

[三、需求分析](#_Toc147820624)

[3.1 项目平台要求](#_Toc147820625)

[3.2 监控需求分析](#_Toc147820626)

[四、系统设计](#_Toc147820627)

[4.1 Docker平台搭建和管理](#_Toc147820628)

[4.2 Prometheus监控和警报系统](#_Toc147820629)

[关键组件详细设计](#_Toc147820630)

[4.3 组件详情](#_Toc147820631)

[4.3.1 alertmanager服务](#_Toc147820632)

[4.3.2 exporter服务](#_Toc147820633)

[4.3.3 prometheus服务](#_Toc147820634)

[4.3.4 grafana服务](#_Toc147820635)

[五、系统实现](#_Toc147820636)

[5. 部署prometheus](#_Toc147820637)

[5.1 服务器物理配置规划](#_Toc147820638)

[5.2 IP地址、主机名称规划](#_Toc147820639)

[5.3 环境准备](#_Toc147820640)

[5.4 软件包下载](#_Toc147820641)

[5.5 解压软件包](#_Toc147820642)

[5.6 部署node_exporter服务](#_Toc147820643)

[5.7 部署alertmanager服务](#_Toc147820644)

[5.8 部署prometheus服务](#_Toc147820645)

[5.9 部署grafana服务](#_Toc147820646)

[5.10 配置grafana对接Prometheus](#_Toc147820647)

[6. 部署docker](#_Toc147820648)

[6.1 基本环境配置](#_Toc147820649)

[6.2 部署docker服务](#_Toc147820650)

[6.3 部署cadvisor容器](#_Toc147820651)

[6.4 配置Prometheus获取cadvisor服务数据](#_Toc147820652)

[6.5 配置docker监控的dashboard](#_Toc147820653)

[6.6 配置docker的告警配置文件](#_Toc147820654)

[7. 监控docker平台服务](#_Toc147820655)

[7.1 监控docker-nginx容器](#_Toc147820656)

[7.2 监控docker-mysql容器](#_Toc147820657)

[8. alertmanager配置163邮箱告警](#_Toc147820658)

[8.1 配置163邮箱](#_Toc147820659)

[8.2 修改alertmanager配置](#_Toc147820660)

[8.3 测试告警服务](#_Toc147820661)

[六、总结](#_Toc147820662)

[参考文献](#_Toc147820663)

 

# 一、设计背景
## 1.1项目简介
本项目旨在创建一个全面的容器化应用程序监控解决方案，基于Prometheus监控Docker平台上的各种服务。在当今的软件开发环境中，容器化技术已成为一种关键的工具，使应用程序能够更快速、可靠地交付和扩展。然而，随着容器数量的增加和复杂性的提高，监控这些容器化服务的性能和健康状态变得至关重要。通过这个项目，我们的目标是提供一个完善的监控解决方案，帮助开发人员、系统管理员和运维团队更好地理解和管理他们的容器化应用程序。这将有助于提高应用程序的稳定性、可用性和性能，从而促进更快速、高效的软件开发和部署。

## 1.2课题目标
随着信息技术的不断发展，容器化技术已经成为现代应用程序开发和部署的主要趋势之一。Docker等容器平台的出现为应用程序的快速交付和部署提供了便利，然而，随之而来的挑战是如何有效地监控和管理这些容器化服务。在这个背景下，本毕业设计项目选择了基于Prometheus监控Docker平台作为研究课题。Prometheus作为一种开源的监控和警报工具，已经在容器环境中得到广泛应用，但如何将其有效地集成到Docker平台中，以确保容器化应用的性能、可用性和可伸缩性，仍然是一个具有挑战性的问题。因此，本项目旨在研究和实现一个可靠的监控解决方案，以满足容器化应用的监控需求。

# 二、设计思路
## 2.1 开发环境与工具
使用到的工具为：

VMware Workstation：VMware Workstation是一款虚拟机软件，可以用于创建和管理虚拟机。它提供了一个可视化界面，使用户可以在单个物理主机上同时运行多个虚拟机，以便在不同的操作系统环境中进行开发、测试或演示。

Centos7.9.2009：Centos7.9.2009是一种基于Linux的操作系统，它被广泛用作服务器操作系统。Centos提供了稳定、安全和可靠的基础系统，可以作为Docker平台的基础环境。

Docker：Docker是一种开源的容器化平台，它使得应用程序和其依赖项可以打包成轻量级、可移植的容器。通过使用Docker，开发人员可以更方便地构建、部署和管理应用程序，实现快速交付和跨平台运行。

Grafana Enterprise：Grafana Enterprise是一款功能强大的数据可视化工具。它提供了丰富的图表、面板和仪表盘，可以将来自各种数据源的指标数据进行可视化展示和交互式探索，帮助用户更好地理解和分析数据。

Alertmanager：Alertmanager是一种用于处理和管理告警的服务。它可以接收来自监控系统（如Prometheus）的告警通知，并根据预定义的规则进行分组、去重、静默或发送给相应的接收者，以便及时响应和解决问题。

Node Exporter：Node Exporter是用于收集主机指标数据的一个Prometheus导出器。它可以在主机上运行，定期采集主机的硬件和操作系统指标，并将其暴露给Prometheus进行监控和存储。

Prometheus：Prometheus是一款开源的监控和警报工具。它提供了强大的时间序列数据采集、存储和查询功能，能够监控多种数据源的指标，并支持设定灵活的告警规则。Prometheus还与Grafana等工具紧密集成，实现数据的可视化和分析。

表 2-1 开发环境

| 工具 | 作用 |
| :---: | :---: |
| VMware Workstation | 创建虚拟机 |
| Centos7.9.2009 | 作为平台基础系统 |
| Docker | 容器平台 |
| grafana-enterprise | 数据可视化 |
| alertmanager | 告警服务 |
| node_exporter | 收集数据服务 |
| prometheus | 监控和警报工具 |

## 2.2 系统版本
以下是当前工具和系统使用的版本信息。

表 2-2 系统版本

| **系统** | **版本** |
| --- | :---: |
| VMware Workstation pro | 16 |
| Centos7 | 7.9.2009 |
| docker | 18.06.3 |
| grafana-enterprise | 9.4.3 |
| alertmanager | 0.25. |
| node_exporter | 1.5.0 |
| prometheus | 2.37.6 |

## 2.3 系统设计
这个系统设计旨在实现全面的监控，包括自身、Docker平台、以及Docker上运行的Nginx和MySQL，并将监控数据可视化通过Grafana展现。系统架构包括Prometheus服务器作为核心组件，负责数据采集和存储，Node Exporter用于监控宿主机系统性能，Docker监控通过cAdvisor或Docker自身的指标监测容器，Nginx Exporter用于收集Nginx服务器的性能数据，而MySQL Exporter则从MySQL数据库中提取性能指标。监控数据汇总后通过Grafana连接到Prometheus数据源，实现实时监控和创建仪表板。监控数据的采集和存储通过Prometheus进行，它定期从各个目标中收集性能数据，并存储在其内部数据库中，提供强大的查询和检索功能。最终，Grafana用于创建自定义仪表板，可视化展示监控数据，帮助实时监测系统和应用程序的性能，以及分析历史趋势。整个系统设计旨在提供全面的监控解决方案，以便有效地管理和优化运行中的自身、Docker平台、Nginx和MySQL等组件。

# 三、需求分析
## 3.1 项目平台要求
Docker平台搭建和管理需求：用户应能够轻松地搭建和管理Docker容器平台，包括容器编排和集群管理平台应支持多种操作系统和云环境，以满足不同用户的需求。

Prometheus集成和配置需求：用户应能够方便地配置Prometheus以监控Docker平台上的容器和服务，支持多个Prometheus实例的集成，以适应不同的监控需求。支持数据收集和存储的灵活性，以满足各种监控数据的需求。

监控规则和告警需求：用户应能够定义自定义监控规则，以检测容器和服务的性能问题，应支持告警规则的设置，以及多种告警通知渠道，如邮件、短信等。用户应能够灵活地调整告警阈值和策略，以适应不同应用的需求。

可视化和仪表板需求：系统应提供直观的仪表板和可视化工具，以帮助用户监控容器化应用程序的性能和状态。仪表板应支持自定义配置，以满足不同用户的可视化需求。用户应能够生成和导出有用的监控报告，以便进行性能分析和决策支持。

安全性和权限需求：系统应具有强大的安全性措施，包括身份验证、授权和数据加密，以保护监控数据的完整性和保密性，应支持多级权限管理，以确保不同用户具有适当的访问权限。

可扩展性和性能需求：系统应具备良好的可扩展性，以适应不断增长的容器数量和监控需求，应能够高效处理大量的监控数据，以保持系统的性能稳定。

文档和培训需求：系统应提供清晰的文档和培训材料，以帮助用户快速上手和有效使用监控工具。

## 3.2 监控需求分析
节点监控：监控Docker主机的硬件资源利用率，包括CPU、内存、磁盘和网络等指标。这可以通过Prometheus的节点导出器（例如node_exporter）实现。

容器监控：监控Docker容器的运行状态和资源消耗情况，包括CPU使用率、内存使用量、网络流量和磁盘I/O等指标。可以使用cAdvisor或者Docker自身的监控API获取容器的指标，并由Prometheus进行采集和存储。

服务健康监测：监控Docker中运行的服务的可用性和性能。可以使用Prometheus的黑盒监测（Blackbox Exporter）对服务进行定期的HTTP或TCP健康检查，并记录响应时间、状态码等指标。

日志监控：集成日志收集工具（如ELK/EFK）以监控Docker容器的日志输出。可以使用Filebeat将容器日志发送到Logstash/Elasticsearch/Fluentd等进行处理和存储，并通过Prometheus的日志导出器进行查询和分析。

告警与警报：设置告警规则，当某个指标超过阈值或触发特定条件时，发送警报给运维人员或团队。Prometheus具有灵活的告警管理机制，可以根据业务需求设置告警规则，并通过邮件、短信、Slack等方式发送警报。

可视化与报表：使用Grafana等工具构建监控仪表盘，将采集到的指标进行可视化展示，以便查看和分析。可以创建图表、面板和报表来监控Docker平台的整体状态和趋势。

# 四、系统设计
## 4.1 Docker平台搭建和管理
设计一个单节点 Docker 平台，用于容器化应用程序的创建、部署和管理，docker系统包括以下主要组件：Docker Engine：负责在单节点上运行容器。Docker Engine 包括 Docker 客户端和 Docker 服务器。Docker 客户端通过 CLI 提供用户与 Docker 的交互接口，而 Docker 服务器实际运行和管理容器。Docker 镜像仓库：用于存储和分享 Docker 镜像，可以选择使用 Docker Hub 或搭建私有镜像仓库。应用程序容器：容器化的应用程序实例，由 Docker Engine 在单节点上运行。

![[1698990536939-a5fe3d59-285d-4623-b1a0-69d8d9990e47.png]]

图4-1 docker系统

## 4.2 Prometheus监控和警报系统
设计一个基于 Prometheus 的监控和警报系统，用于实时监测应用程序和基础设施的性能，同时能够生成警报并记录历史数据以进行分析，系统包括以下主要组件：

PrometheusServer：Prometheus服务器用于定期从监控目标采集数据，存储时间序列数据，并提供查询接口供用户和其他组件使用。

监控目标：这些是需要监控的应用程序、主机、容器等，它们会向Prometheus提供性能数据。可以使用Prometheus的客户端库或者导出器（exporter）来实现监控目标与Prometheus的集成。

Alertmanager：Alertmanager负责管理警报规则和发送警报通知。它能够根据定义的规则对收集到的数据进行警报匹配，并将警报通知发送到各种通知通道，如电子邮件、Slack、PagerDuty等。

Grafana：Grafana是一个可视化工具，它可以与Prometheus集成，用于创建仪表板和图表以可视化监控数据。

数据存储：Prometheus默认使用本地存储，但也可以配置远程存储，如长期存储到远程数据库。

![[1698990537220-62f8fff0-34b3-42c6-8579-b9295d8137c0.png]]

图4-2 监控系统

### 4.2.1关键组件详细设计
PrometheusServer：部署PrometheusServer，并配置监控目标的终端点。创建监控规则，用于定义需要收集的监控指标。配置数据保留策略和存储时间段，以控制数据的保留时间。

监控目标：在应用程序、主机、容器等上部署Prometheus客户端库或使用Exporter来暴露监控指标。配置监控目标的相关指标和标签，以便Prometheus可以采集数据。

Alertmanager：定义警报规则，包括警报条件、接收通知的通道以及通知模板。

配置接收通知的集成，如电子邮件服务器或聊天平台。

Grafana：部署Grafana服务器，并配置Prometheus数据源。创建仪表板和图表，以可视化监控数据。配置警报通知，以便在Grafana中接收警报通知。

![[1698990537548-7f289e07-41cb-4f2b-b973-1a042979f2fb.png]]

图 4-3关键组件

## 4.3 组件详情
### 4.3.1 alertmanager服务
Alertmanager是一个用于处理监控警报的重要开源工具，广泛用于与Prometheus监控系统集成，以实现更强大的监控和警报管理 ，其主要任务是接收、路由和发送警报通知，确保团队能够及时采取适当的措施来解决问题 ，Alertmanager并不负责生成警报，而是从Prometheus服务器接收已经生成的警报数据 ，这些警报数据包含有关监测系统检测到的问题的信息，例如资源使用率异常、服务不可用等 ，Alertmanager通过丰富的配置选项可以轻松地处理这些数据，使其具有高度的灵活性和可定制性 ，一项关键功能是警报的路由和分组 ，Alertmanager允许用户定义路由规则，这些规则可以基于警报的标签和注释来制定 ，这使得警报可以根据其严重性、来源或其他特征被路由到不同的通知渠道 ，例如，严重性较低的警报可以通过电子邮件发送给开发团队，而严重性较高的警报则可能通过短信或PagerDuty通知值班人员 ，Alertmanager支持抑制功能，可避免不必要的警报通知，这对于管理大量警报的环境非常重要 ，抑制规则可以临时或永久地停止特定警报的通知，从而降低噪音和干扰 ，Alertmanager还提供了警报的分组功能，以帮助组织和理解问题的关联性 ，相关的警报可以被分组在一起，以便更清晰地呈现问题的整体情况，这有助于团队更快地识别和处理问题 ，Alertmanager是一个强大的工具，可协助运维团队有效地管理监控警报，提高系统的可用性和稳定性 ，它的灵活性、通知集成能力以及支持抑制和分组的功能，使其成为现代监控系统中不可或缺的一部分 。

![[1698990537938-ec113f71-ec23-4186-9d70-608ba79d3311.png]]

图 4-4 alertmanager服务架构

### 4.3.2 exporter服务
Node Exporter是一个开源的Prometheus监控工具，用于收集和暴露有关主机操作系统的各种系统性能指标 ，它是Prometheus生态系统的一部分，旨在帮助系统管理员、运维团队和开发人员监视和诊断主机的性能问题 ，Node Exporter可以在目标主机上运行为一个独立的进程或服务，并暴露HTTP端点，Prometheus服务器可以定期拉取这些端点的数据 ，Node Exporter会收集各种系统信息，如CPU使用率、内存使用、磁盘空间、网络流量、负载等，并将其以Prometheus友好的格式暴露给Prometheus服务器 ，这些指标可以用于创建警报规则、图表和仪表板，以便实时监视和分析主机的性能 ，Node Exporter支持多种操作系统，包括Linux、Unix和Windows，因此可以广泛用于不同类型的主机 ，它还提供了各种插件和扩展点，允许用户自定义监控和数据收集，以满足特定需求 ，Node Exporter是一个强大的系统性能监控工具，通过提供丰富的性能指标，有助于追踪和解决主机上的性能问题，提高系统的稳定性和可用性 ，它与Prometheus的无缝集成使得在分布式环境中管理和监控主机变得更加容易 。

![[1698990538251-a412d7a1-197d-4b1e-b14d-61309ad5a2d4.png]]

图4-5 node_exporter架构

### 4.3.3 prometheus服务
Prometheus是一个开源的系统和服务监控工具，旨在帮助运维团队和开发人员收集、存储和分析应用程序和系统的性能数据 ，Prometheus的核心设计理念是通过拉取方式定期获取监控指标，以及使用多维数据模型来支持灵活的查询和警报 ，Prometheus能够收集各种应用程序和系统的性能数据，包括CPU使用率、内存消耗、网络流量、磁盘容量等 ，它通过HTTP端点暴露这些指标，以便Prometheus服务器可以定期拉取数据 ，这种拉取模型使得Prometheus适用于多种不同的环境和语言，因为被监控的应用程序只需要提供一个HTTP端点来暴露指标 ，Prometheus提供了灵活的查询语言（PromQL），使用户能够实时分析和查询监控数据，以便识别问题并进行趋势分析 ，此外，Prometheus还支持创建警报规则，可用于实时通知运维人员关于问题的发生 ，Prometheus是一个可扩展的监控系统，可以轻松集成各种插件和第三方工具，以满足不同监控需求 ，它还有一个活跃的社区支持，因此用户可以获得广泛的文档和支持资源，帮助他们有效地配置和使用Prometheus ，Prometheus是一个功能强大的监控和警报工具，广泛应用于云原生、容器化和微服务架构的环境中，帮助用户实时监视系统性能，提高可用性和可靠性 ，它的灵活性、可扩展性和强大的查询语言使其成为监控领域的重要工具 。

![[1698990538584-edc13615-f869-445e-b6c6-d2c1d2ece6d8.png]]

图 4-6 Prometheus服务架构

### 4.3.4 grafana服务
Grafana是一款流行的开源数据可视化和监控仪表板工具，广泛用于展示和分析各种数据源的信息 ，其主要用途是将时间序列数据和指标转化为交互式、直观的图表和仪表板，以帮助用户更好地理解和监视系统性能、应用程序健康状况以及各种业务指标 ，Grafana支持多种数据源，包括Prometheus、InfluxDB、Elasticsearch、MySQL等，使用户能够从不同的数据存储中提取和可视化数据 ，它提供了一个友好的Web界面，用户可以通过拖放式操作来创建和自定义仪表板，无需编写复杂的代码 ，一个强大的特性是Grafana的可视化插件生态系统，允许用户自定义图表和仪表板的外观和功能，以满足不同的需求 ，用户可以选择从丰富的图表类型和面板选项中进行选择，以创建精美的可视化效果 ，Grafana还支持警报规则，用户可以基于图表上的数据设置警报条件，并在达到特定阈值时接收通知 ，这有助于实时监控和及时响应问题 ，总的来说，Grafana是一个功能强大的工具，用于创建仪表板和可视化数据，帮助用户实时监控和分析各种数据源 ，它的广泛支持的数据源和丰富的可视化选项使其成为监控和数据分析领域的首选工具之一，受到许多开发人员和运维团队的青睐 。

![[1698990538980-7bd3e20a-597f-49ca-9ece-918b4bdbf99a.png]]

图4-7 grafana架构

# 五、系统实现
## 5.1 部署prometheus
### 5.1.1 服务器物理配置规划
服务器的硬件配置如下。

表 5-1 配置规划

| **角色** | **系统架构** | **内存大小** | **核心数量** |
| :---: | :---: | :---: | :---: |
| prometheus | Centos7.9.2009 | 4G | 4cpu |
| docker | Centos7.9.2009 | 8G | 8cpu |

### 5.1.2 IP地址、主机名称规划
服务器的IP地址规划如下。

表 5-2 主机ip地址

| **IP 地址** | **主机名** | **角色** |
| :---: | :---: | :---: |
| 网卡1：192.168.100.250 nat模式 | Prometheus | 监控服务器 |
| 网卡1：192.168.100.251 nat模式 | Docker | 容器服务器 |

 

### 5.1.3 环境准备
修改服务器名称，关闭防火墙，并创建用于运行Prometheus的用户。

# Prometheus节点

#修改服务器名称

hostnamectl set-hostname Prometheus

#关闭防火墙

systemctl stop firewalld

systemctl disable firewalld

setenforce 0

sed -i "s/SELINUX=.*/SELINUX=disabled/g" /etc/selinux/config

#创建prometheus工作目录

mkdir -p /opt/prometheus/

#创建运行Prometheus服务的用户

useradd -M -s /usr/sbin/nologin prometheus

 

### 5.1.4 软件包下载
去各组件的官方网站下载项目对应的软件包。

# Prometheus节点

#Prometheus下载

wget [https://ghproxy.com/https://github.com/prometheus/prometheus/releases/download/v2.37.6/prometheus-2.37.6.linux-amd64.tar.gz](https://ghproxy.com/https://github.com/prometheus/prometheus/releases/download/v2.37.6/prometheus-2.37.6.linux-amd64.tar.gz)

#alertmanager下载

wget [https://ghproxy.com/https://github.com/prometheus/alertmanager/releases/download/v0.25.0/alertmanager-0.25.0.linux-amd64.tar.gz](https://ghproxy.com/https://github.com/prometheus/alertmanager/releases/download/v0.25.0/alertmanager-0.25.0.linux-amd64.tar.gz)

#node_exporter下载

wget [https://ghproxy.com/https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz](https://ghproxy.com/https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz)

#grafana下载

wget [https://dl.grafana.com/enterprise/release/grafana-enterprise-9.4.3.linux-amd64.tar.gz](https://dl.grafana.com/enterprise/release/grafana-enterprise-9.4.3.linux-amd64.tar.gz)

### 5.1.5 解压软件包
解压下载的软件包。

# Prometheus节点

#解压所有软件包

tar -zxvf alertmanager-0.25.0.linux-amd64.tar.gz -C /opt/prometheus/

tar -zxvf grafana-enterprise-9.4.3.linux-amd64.tar.gz -C /opt/prometheus/

tar -zxvf prometheus-2.37.6.linux-amd64.tar.gz -C /opt/prometheus/

tar -zxvf node_exporter-1.5.0.linux-amd64.tar.gz -C /opt/prometheus/

#重新命名软件包

mv /opt/prometheus/node_exporter-1.5.0.linux-amd64/ /opt/prometheus/node_exporter

mv /opt/prometheus/alertmanager-0.25.0.linux-amd64/  /opt/prometheus/alertmanager

mv /opt/prometheus/grafana-9.4.3/  /opt/prometheus/grafana

mv /opt/prometheus/prometheus-2.37.6.linux-amd64/  /opt/prometheus/prometheus

### 5.1.6 部署node_exporter服务
开始部署node_exporter服务，用于当前服务器的数据采集。

# Prometheus节点

#编写systemd文件

cat >> /usr/lib/systemd/system/node_exporter.service << EOF

[Unit]

Description=node_exporter

Documentation=https://prometheus.io/

After=network.target

[Service]

User=prometheus

Group=prometheus

ExecStart=/opt/prometheus/node_exporter/node_exporter

Restart=on-failure

[Install]

WantedBy=multi-user.target

EOF

#递归的将用户和组改为prometheus

chown -R prometheus:prometheus /opt/prometheus

#重载systemd配置，并启动node_exporter服务

systemctl daemon-reload

systemctl start node_exporter

systemctl enable node_exporter

查看node_exporter状态，浏览器访问http://prometheus_ip:9100/metrics查看node_exporter服务数据。

![[1698990539439-f55891c1-e072-4ba1-a28f-898fe3ab0670.png]]

图 5-1查看node_exporter状态

![[1698990539741-440576b3-1d4f-4d77-ac22-a54b7d6faeba.png]]

图 5-2 查看node_exporter网页数据

### 5.1.7 部署alertmanager服务
部署告警服务器。

# Prometheus节点

#编写systemd文件

cat >> /usr/lib/systemd/system/alertmanager.service << EOF

[Unit]

Description=Alert Manager

Wants=network-online.target

After=network-online.target

 

[Service]

Type=simple

User=prometheus

Group=prometheus

ExecStart=/opt/prometheus/alertmanager/alertmanager \

  --config.file=/opt/prometheus/alertmanager/alertmanager.yml \

  --storage.path=/opt/prometheus/alertmanager/data

 

Restart=always

 

[Install]

WantedBy=multi-user.target

EOF

#递归的将用户和组改为prometheus

chown -R prometheus:prometheus /opt/prometheus

#重载systemd配置，并启动alertmanager服务

systemctl daemon-reload

systemctl start alertmanager

systemctl enable alertmanager

查看alertmanager状态，浏览器访问http://prometheus_ip: 9093/#/status查看alertmanager服务状态。

![[1698990540056-61153799-64e2-4592-8d32-cf10fa7717b2.png]]

图 5-3查看alertmanager状态

![[1698990540410-55c99cdd-2db9-47b1-8d7f-37de3f00d16d.png]]

图5-4 alertmanager服务状态

### 5.1.8 部署prometheus服务
部署普罗米修斯服务，修改对应的配置文件，正式启动监控服务。

# Prometheus节点

#编写systemd文件

cat >> /usr/lib/systemd/system/prometheus.service << EOF

[Unit]

Description=Prometheus Server

Documentation=https://prometheus.io/docs/introduction/overview/

After=network-online.target

 

[Service]

Type=simple

User=prometheus

Group=prometheus

Restart=on-failure

ExecStart=/opt/prometheus/prometheus/prometheus \

  --config.file=/opt/prometheus/prometheus/prometheus.yml \

  --storage.tsdb.path=/opt/prometheus/prometheus/data \

  --storage.tsdb.retention.time=60d \

  --web.enable-lifecycle

 

[Install]

WantedBy=multi-user.target

EOF

#配置告警规则

cat >> /opt/prometheus/prometheus/alert.yml << EOF

groups:

- name: Prometheus alert

  rules:

  # 对任何实例超过30s无法联系的情况发出警报

  - alert: 服务告警

    expr: up == 0

    for: 30s

    labels:

      severity: critical

    annotations:

      instance: "{{ $labels.instance }}"

      description: "{{ $labels.job }} 服务已关闭"

EOF

#修改prometheus.yml配置

cat > /opt/prometheus/prometheus/prometheus.yml << EOF

# my global config

global:

  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.

  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.

  # scrape_timeout is set to the global default (10s).

 

# Alertmanager configuration

alerting:

  alertmanagers:

    - static_configs:

        - targets:

          - localhost:9093

 

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.

rule_files:

  - "alert.yml"

  # - "second_rules.yml"

 

# A scrape configuration containing exactly one endpoint to scrape:

# Here it's Prometheus itself.

scrape_configs:

  # The job name is added as a label  to any timeseries scraped from this config.

  - job_name: "prometheus"

 

    # metrics_path defaults to '/metrics'

    # scheme defaults to 'http'.

 

    static_configs:

      - targets: ["localhost:9090"]

  - job_name: 'alertmanager'

    scrape_interval: 15s

    static_configs:

    - targets: ['localhost:9093']

 

  - job_name: 'node-exporter'

    scrape_interval: 15s

    static_configs:

    - targets: ['localhost:9100']

      labels:

        instance: Prometheus服务器

EOF

#递归的将用户和组改为prometheus

chown -R prometheus:prometheus /opt/prometheus

#重载systemd配置，并启动prometheus服务

systemctl daemon-reload

systemctl start prometheus

systemctl enable prometheus

查看prometheus状态，浏览器访问http://prometheus_ip: 9090查看三个服务状态是否正常。

![[1698990540694-bb5fcd5b-e7ef-4168-991b-f012ae2fa590.png]]

图 5-5 查看prometheus状态

![[1698990541046-dea1eb5f-5ede-4e90-b8dd-7baf9928109e.png]]

图 5-6查看三个服务状态

### 5.1.9 部署grafana服务
部署grafana实现数据的可视化，对接普罗米修斯。

# Prometheus节点

#编写systemd文件

cat >> /usr/lib/systemd/system/grafana-server.service << EOF

[Unit]

Description=Grafana server

Documentation=http://docs.grafana.org

[Service]

Type=simple

User=prometheus

Group=prometheus

Restart=on-failure

ExecStart=/opt/prometheus/grafana/bin/grafana-server \

  --config=/opt/prometheus/grafana/conf/defaults.ini \

  --homepath=/opt/prometheus/grafana

[Install]

WantedBy=multi-user.target

EOF

#递归的将用户和组改为prometheus

chown -R prometheus:prometheus /opt/prometheus

#重载systemd配置，并启动grafana服务

systemctl daemon-reload

systemctl start grafana-server

systemctl enable grafana-server

查看grafana状态，浏览器访问[http://prometheus_ip:3000](http://prometheus_ip:3000)可以看到grafana的登录页面。

![[1698990541396-b215ae3c-fefd-4427-9f33-395fa616acfb.png]]

图5-7 查看grafana状态

![[1698990541798-08c84bd3-d221-4f7f-9c06-fd64839500d8.png]]

图5-8 grafana的登录页面

### 5.1.10 配置grafana对接Prometheus
浏览器访问[http://prometheus_ip:3000](http://prometheus_ip:3000)登录grafana,账号密码默认为admin/admin，登录后提示修改密码。

![[1698990542342-68789d6c-c8a5-4cfd-8752-cae94a23b992.png]]

图5-9 登录配置

修改完密码后，我们配置grafana的数据源为Prometheus，点击左下角“设置图标”点击Data sources 点击Prometheus。

![[1698990542810-5f0a88e0-31fa-41b3-bc70-cf9301ee5811.png]]

图5-10 配置数据源

设置Prometheus服务地址，这里两个服务都在同一台主机可以直接使用localhost，点击save&test保存测试能否成功。

![[1698990543323-ce043214-1dfb-41aa-9899-b013cbfff144.png]]

图5-11 配置Prometheus地址

![[1698990543626-397ce1ed-352c-4aeb-a803-313d7b7f24ea.png]]

图5-12 保存测试数据源

设置好数据源后，我们需要设置node_exporter的dashboard方便我们观察到监控数据。

![[1698990543973-d2e08b95-baa5-47da-bea8-910d18292a63.png]]

图5-13 进入配置dashboard界面

![[1698990544293-8cd2e1c9-e9be-408a-aaa2-325043d5172b.png]]

图5-14 配置dashboard-json

关于配置dashboard的方法有两种，一种是上传json文件，一种是以id的方式配置，获取id和json文件的方式需要访问[https://grafana.com/grafana/dashboards/](https://grafana.com/grafana/dashboards/)因为网络原因，我们此次使用提前准备的json文件配置。

![[1698990544771-cf53540b-492b-43a7-8c57-675eed83b014.png]]

图5-15 node_exporter-dashboard配置

![[1698990545248-e4846a57-7544-4f57-a44b-4effd99e79ac.png]]

图5-16 可以复制id或者下载json文件

上传json文件后，进行基本的设置，设置完成后点击import，即可配置完毕，查看到本机linux服务器数据仪表盘。

![[1698990545785-163a25b5-8ce0-4b42-8933-42dbb80112bb.png]]

图5-17基本配置

![[1698990546284-4bb28826-fc97-4252-be22-37fef904b716.png]]

图5-18 Linux服务器数据dashboard

## 5.2 部署docker
### 5.2.1 基本环境配置
配置docker主机的主机名，和基本的环境配置，关闭防火墙等。

#docker节点执行

#修改主机名

hostnamectl set-hostname docker-server

#关闭防火墙

systemctl stop firewalld

systemctl disable firewalld

setenforce 0

sed -i "s/SELINUX=.*/SELINUX=disabled/g" /etc/selinux/config

### 5.2.2 部署docker服务
使用docker提供的网络源安装docker服务器，并且配置阿里云镜像加速器。

# docker节点执行

#安装必要的一些系统工具

sudo yum install -y yum-utils device-mapper-persistent-data lvm2

yum-config-manager --add-repo [http://download.docker.com/linux/centos/docker-ce.repo](http://download.docker.com/linux/centos/docker-ce.repo)

#加载元数据

sudo yum makecache fast

#安装docker

yum -y install docker-ce-18.06.3.ce-3.el7

yum -y install docker-compose

mkdir -p /etc/docker

cat >> /etc/docker/daemon.json <<EOF

{

        "registry-mirrors":["https://fem5eo07.mirror.aliyuncs.com"]

}

EOF

#启动docker

systemctl start docker

systemctl enable docker

### 5.2.3 部署cadvisor容器
部署docker的数据采集容器。

#docker节点执行

#拉取镜像文件

docker pull google/cadvisor

#编写docker-compose.yaml文件

cat >> docker-compose.yaml << EOF

version: "3.0"

services:

  monitor:

    container_name: cadvisor

    image: google/cadvisor

    restart: always

    volumes:

      - /:/rootfs:ro

      - /var/run:/var/run:rw

      - /sys:/sys:ro

      - /var/lib/docker/:/var/lib/docker:ro

    ports:

      - 8080:8080

EOF

#启动容器

docker-compose up -d

#查看状态

[root@docker-server ~]# docker-compose ps

  Name                Command               State           Ports

--------------------------------------------------------------------------

cadvisor   /usr/bin/cadvisor -logtostderr   Up      0.0.0.0:8080->8080/tcp.

  使用浏览器访问cadvisor服务http://[192.168.100.251:8080/metrics](http://192.168.100.251:8080/metrics)，查看metrics数据。

![[1698990546744-15442f1d-5e4c-409b-8cc6-0926a33248a2.png]]

图5-19 metrics数据

 

### 5.2.4 配置Prometheus获取cadvisor服务数据
Cadvisor容器获取了docker容器平台的大部分数据，并且数据格式符合Prometheus的规范，Prometheus可以直接获取数据。

#Prometheus节点执行

cat >>/opt/prometheus/prometheus/prometheus.yml<< EOF

  - job_name: 'Docker'

    scrape_interval: 15s

    static_configs:

    - targets: ['192.168.100.251:8080']

      labels:

        instance: Docker服务器

EOF

#热加载配置

curl -X POST http://localhost:9090/-/reload

访问http://192.168.100.250:9090/targets?search=查看Prometheus是否成功添加了docker平台数据，

![[1698990547149-f964a74a-f072-45ce-86fb-79c2b0810277.png]]

图5-20 查看状态

 

### 5.2.5 配置docker监控的dashboard
访问grafana的dashboard-import，导入docker-dashboard的dashboard-ID号11600。

![[1698990547624-87eec92d-0409-4834-91ba-f519375ba54e.png]]

图5-21添加docker-dashboard JSON文件

![[1698990548121-25383936-bdcc-49d8-bcef-c5e86acc51e6.png]]

图5-22 查看dashboard

 

### 5.2.6 配置docker的告警配置文件
修改普罗米修斯的配置文件，编写一个告警规则。

#Prometheus节点执行

#创建文件夹

mkdir -p /opt/prometheus/prometheus/rules/

#编辑prometheus.yml

vi prometheus.yml

#添加红色配置

rule_files:

  - "alert.yml"

  - "rules/*.yml"

#编写告警配置文件

cat >> /opt/prometheus/prometheus/rules/docker.yml << EOF

groups:

- name: DockerContainers

  rules:

  - alert: Containerkilled

    expr: time() - container_last_seen > 60

    for: 0m

    labels:

      severity: warning

    annotations:

      isummary: "Docker容器被杀死 容器: $labels.instance"

      description: "{{ $value }}个容器消失了"

  - alert: ContainerAbsent

    expr: absent(container_last_seen)

    for: 5m

    labels:

      severity: warning

    annotations:

      summary: "无容器 容器: $labels.instance"

      description: "5分钟检查容器不存在，值为: {{ $value }}"

EOF

#重新加载配置

chown -R prometheus:prometheus /opt/Prometheus

curl -X POST http://localhost:9090/-/reload

访问Prometheus查看是否成功。

![[1698990548623-495b891d-7806-4de1-ae1d-d66d5560feeb.png]]

图5-23 docker告警

 

## 5.3 监控docker平台服务
### 5.3.1 监控docker-nginx容器
#### 5.3.1.1部署nginx容器
部署用于测试的nginx容器。

#docker节点

#创建目录

mkdir -p /root/nginx/conf.d

#编写nginx配置文件

cat >> /root/nginx/conf.d/server.conf << EOF

server {

    listen       80;

    server_name  localhost;

 

    location / {

        root   /usr/share/nginx/html;

        index  index.html index.htm;

    }

 

    error_page   500 502 503 504  /50x.html;

    location = /50x.html {

        root   /usr/share/nginx/html;

}

 

}

EOF

#编写nginx的docker-compose.yaml

Cat >>/root/nginx/docker-compose.yaml<< EOF

version: "3.0"

services:

  nginx:

    image: nginx

    container_name: nginx

    ports:

     - 80:80

    volumes:

     - /root/nginx/conf.d/:/etc/nginx/conf.d/

     - /root/nginx/html:/usr/share/nginx/html

     - /root/nginx/log:/var/log/nginx

EOF

#检查是否安装with-http_stub_status_module模块

docker-compose exec  nginx  nginx -V |grep -o with-http_stub_status_module

with-http_stub_status_module

#开启stub_status配置

cat > /root/nginx/conf.d/server.conf << EOF

server {

    listen       80;

    server_name  localhost;

 

    location /stub_status {

      stub_status on;

      access_log off;

      #allow nginx_exporter的ip;

      allow 0.0.0.0/0;

      deny all;

 

 

    }

 

 

 

    location / {

        root   /usr/share/nginx/html;

        index  index.html index.htm;

    }

 

    error_page   500 502 503 504  /50x.html;

    location = /50x.html {

        root   /usr/share/nginx/html;

}

}

EOF

#nginx重新加载配置文件

docker exec -it nginx nginx -s reload

#检查是否正常开启

[root@docker-server nginx]# curl 192.168.100.251/stub_status

Active connections: 1

server accepts handled requests

 1 1 1

Reading: 0 Writing: 1 Waiting: 0

#### 5.3.1.2部署nginx-exporter
部署用于收集nginx数据的exporter

#docker节点

#创建目录

mkdir -p /root/nginx/mon

#编写nginx-exporter的docker-compose.yaml

cat >>/root/nginx/mon/docker-compose.yaml<< EOF

version: '3.0'

services:

  nginx_exporter:

    image: nginx/nginx-prometheus-exporter:0.11

    container_name: nginx_exporter

    hostname: nginx_exporter

    command:

     - '-nginx.scrape-uri=http://192.168.100.251/stub_status'

    restart: always

    ports:

    - "9113:9113"

EOF

#启动nginx_exporter

docker-compose up -d

#检查状态

[root@docker-server mon]# docker-compose ps

     Name                   Command               State           Ports

--------------------------------------------------------------------------------

nginx_exporter   /usr/bin/nginx-prometheus- ...   Up      0.0.0.0:9113->9113/tcp

  添加到prometheus配置。

#prometheus节点

#加入以下配置

vi /opt/prometheus/prometheus/prometheus.yml

  - job_name: 'Docker-nginx'

    scrape_interval: 15s

    static_configs:

    - targets: ['192.168.100.251:9113']

      labels:

        instance: Docker服务器nginx

#热加载配置

curl -X POST http://localhost:9090/-/reload

访问grafana的dashboard-import，导入nginx-dashboard-ID号12708，即可添加nginx的dashboard。

![[1698990549123-024dd6ff-c150-4afb-944c-0ae45da2ce6e.png]]

图5-24 nginx-dashboard

#### 5.3.1.3配置nginx的告警设置
配置nginx的邮箱告警规则。

#prometheus节点

#编写告警配置文件

cat >>/opt/prometheus/prometheus/rules/nginx.yml<< EOF

groups:

- name: Nginx

  rules:

  - alert: NginxDown

    expr: nginx_up == 0

    for: 30s

    labels:

      severity: critical

    annotations:

      isummary: "nginx异常,实例:{{ $labels.instance }}"

      description: "{{ $labels.job }} nginx已关闭"

EOF

#重新加载配置

chown -R prometheus:prometheus /opt/prometheus

curl -X POST http://localhost:9090/-/reload

访问Prometheus查看是否成功。

![[1698990549555-9376aa5e-77e7-460f-96a9-f02a0faf568d.png]]

图5-25 mysql告警

### 5.3.2 监控docker-mysql容器
#### 5.3.2.1部署mysql容器
部署mysql测试容器。

#docker节点

#创建目录

mkdir /root/mysql

#编写docker-compose.yaml

cat >> docker-compose.yaml<< EOF

version: '3.0'

services:

  db:

    image: mysql

    restart: always

    container_name: mysql

    ports:

      - 3306:3306

    environment:

      MYSQL_DATABASE: prometheus

      MYSQL_USER: user1

      MYSQL_PASSWORD: '000000'

      MYSQL_RANDOM_ROOT_PASSWORD: '000000'

EOF

#部署mysql

docker-compose up -d

#查看状态

docker-compose ps

[root@docker-server mysql]# docker-compose ps

Name              Command             State                 Ports

-------------------------------------------------------------------------------

mysql   docker-entrypoint.sh mysqld   Up      0.0.0.0:3306->3306/tcp, 33060/tcp

#### 5.3.2.2部署mysqld-exporter‘
部署用于采集mysql容器数据的exporter。

#docker节点执行

#创建工作目录

mkdir /root/mysql/mon

#编写docker-compose.yaml

cat >> docker-compose.yaml<< EOF

version: '3.0'

services:

  mysql-exporter:

    image: prom/mysqld-exporter

    container_name: mysqld-exporter

    restart: always

    command:

     - '--collect.info_schema.processlist'

     - '--collect.info_schema.innodb_metrics'

     - '--collect.info_schema.tablestats'

     - '--collect.info_schema.tables'

     - '--collect.info_schema.userstats'

     - '--collect.engine_innodb_status'

    environment:

     - DATA_SOURCE_NAME=prometheus:000000@(192.168.100.251:3306)/

    ports:

     - 9104:9104

EOF

#部署mysqld-exporter

docker-compose up -d

#查看状态

[root@docker-server mon]# docker-compose ps

     Name                    Command               State           Ports

---------------------------------------------------------------------------------

mysqld-exporter   /bin/mysqld_exporter --col ...   Up      0.0.0.0:9104->9104/tcp

添加到prometheus配置。

#prometheus节点

#加入以下配置

vi /opt/prometheus/prometheus/prometheus.yml

  - job_name: 'Docker-mysql'

    scrape_interval: 15s

    static_configs:

    - targets: ['192.168.100.251:9104']

      labels:

        instance: Docker服务器mysql

#热加载配置

curl -X POST http://localhost:9090/-/reload

  访问grafana的dashboard-import，导入7362和9625，分别为mysql数据库，和mysql数据表的dashboard-ID号。

![[1698990549906-d94486f8-5b29-48d9-b2be-6b9b4476515a.png]]

图5-26 数据库dashboard

![[1698990550421-be8e9112-abbe-4f7e-89ec-a7244eef4238.png]]

图 5-27数据表dashboard

#### 5.3.2.3配置mysql的告警设置
配置mysql的邮箱告警配置文件。

#prometheus节点

#编写告警配置文件

cat >>/opt/prometheus/prometheus/rules/mysql.yml<< EOF

groups:

- name: DockerContainers

  rules:

  - alert: Containerkilled

    expr: time() - container_last_seen > 60

    for: 0m

    labels:

      severity: warning

    annotations:

      isummary: "Docker容器被杀死 容器: {{ $labels.instance }}"

      description: "{{ $value }}个容器消失了"

 

  - alert: ContainerAbsent

    expr: absent(container_last_seen)

    for: 5m

    labels:

      severity: warning

    annotations:

      summary: "无容器 容器: {{ $labels.instance }}"

      description: "5分钟检查容器不存在，值为: {{ $value }}"

EOF

#重新加载配置

chown -R prometheus:prometheus /opt/Prometheus

curl -X POST http://localhost:9090/-/reload

访问Prometheus查看是否成功。

![[1698990550930-50b98d77-ff3a-416c-aaba-43462639ab2a.png]]

图5-28 mysql告警

## 5.4 alertmanager配置163邮箱告警
### 5.4.1 配置163邮箱
登录配置163邮箱开启POP3/SMTP服务。

![[1698990551473-abee06f0-800c-4952-9cca-ad67762523ec.png]]

图5-29配置开启服务

![[1698990552062-ce2e9f35-1240-4ccd-b8a2-837a5b608925.png]]

图5-30 开启服务

开启后会获得一个授权码，后续我们将使用这个授权码配置alertmanager.yml文件对接我们的163邮箱。

### 5.4.2 修改alertmanager配置
修改alertmanager配置文件，将我们的邮箱添加到alertmanager。

#Prometheus节点执行

cat  >>alertmanager.yml<< EOF

global:

#配置告警邮箱

  smtp_smarthost: 'smtp.163.com:465'

#163服务器

  smtp_from: 'a351719672@163.com'

#发邮件的邮箱

  smtp_auth_username: 'a351719672@163.com'

#发邮件的邮箱用户名

  smtp_auth_password: 'IDMPGUMPWAAJPGKC'

#发邮件的邮箱密码

  smtp_require_tls: false

#进行tls验证

 

route:

  group_by: ['alertname']

  group_wait: 30s

  group_interval: 5m

  repeat_interval: 1h

  receiver: 'email'

#全局报警组，这个参数必选

receivers:

  - name: 'email'

    email_configs:

    - to: '351719672@qq.com'

#收邮件的邮箱

inhibit_rules:

  - source_match:

      severity: 'critical'

    target_match:

      severity: 'warning'

    equal: ['alertname', 'dev', 'instance']

EOF

#热加载配置

curl -X POST http://localhost:9093/-/reload

### 5.4.3 测试告警服务
测试各个告警配置是否能成功生效

#### 5.4.3.1 Nginx告警测试
停止nginx,测试nginx存活检测是否正常.

#docker节点执行

#测试nginx存活检测是否正常

[root@docker-server ~]# docker stop nginx

nginx

  实例转为pending，进而变而FIRING。

![[1698990552505-2a59f032-5ff9-4066-910a-6db7658ea812.png]]

图5-31  nginx状态pending

![[1698990552915-1ec72406-f4a6-4fef-9bf4-93474d55264b.png]]

图5-32 nginx状态firing

可以看到邮箱正常接收到了nginx的告警。

![[1698990553326-e353d2d1-1cfd-4990-aefa-8969be75b10b.jpeg]]

图 5-33 邮箱nginx告警

#### 5.4.3.2 Mysql告警测试
测试mysql存活检测是否正常

#docker节点执行

[root@docker-server ~]# docker stop mysql

Mysql

实例转为pending，进而变而FIRING。

![[1698990553921-3bfc7aee-f4ed-4ed0-ba8a-5b3529ff0680.png]]

图5-34  mysql状态pending

![[1698990552915-1ec72406-f4a6-4fef-9bf4-93474d55264b.png]]

图5-35  mysql状态firing

可以看到邮箱正常接收到了mysql的告警。

![[1698990554203-695a95be-6d53-430b-bf16-6eb01b3db44f.png]]

图5-36  邮箱mysql告警

#### 5.4.3.3 docker容器告警测试
测试docker检测是否正常

#docker节点执行

#测试docker检测是否正常

[root@docker-server cadvisor]# docker stop cadvisor

cadvisor

实例转为pending进而变而FIRING。

![[1698990554703-44313edf-f6f4-47a9-8412-87ca6846f252.png]]

图5-37  docker状态pending

![[1698990555155-f22d1211-1281-49b6-8a11-35dc7cc61be7.png]]

图5-38 docker状态firing

可以看到邮箱正常接收到了docker的告警。

![[1698990555696-c521a440-edd7-46ba-8732-da74f8522170.png]]

图5-39 邮箱docker告警

六、总结

我的毕业设计主题是基于Prometheus监控Docker平台。在这个项目中，我成功地搭建了一个Prometheus监控平台，并部署了一个Docker容器平台。通过Prometheu这个平台，我能够监控各种容器化服务的性能和运行状态。我首先建立了一个Docker环境，包括安装和配置Docker引擎、创建容器、以及运行不同的服务。这为后续的监控工作提供了必要的基础。接着，我配置了Prometheus，设置了数据采集的目标，包括各个Docker容器的指标和性能参数。通过Prometheus的数据采集和存储功能，我能够实时监控Docker平台上的各种服务。为了更有效地管理和分析监控数据，我编写了一些监控规则和告警规则。这些规则能够帮助我及时发现并处理潜在的问题，确保Docker平台的稳定性和可靠性。通过这些规则，我能够及时响应异常情况，降低了系统故障的风险。通过这个毕业设计项目，我成功地搭建了一个基于Prometheus的监控系统，用于监控Docker平台上的各种服务。这个项目不仅提高了我对容器化技术和监控工具的理解，还锻炼了我在系统管理和故障处理方面的能力。我相信这个项目为我未来的职业发展打下了坚实的基础。感谢我的指导老师在整个毕业设计过程中的悉心指导和支持。老师的专业知识和经验对我的项目起到了关键作用，让我能够顺利完成这项任务。在老师的启发下，我不仅获得了宝贵的学术经验，还培养了解决问题和独立思考的能力。老师的鼓励和指导对我来说意义重大，我会铭记在心，感激不已。希望能够继续在您的指导下不断进步，为未来的学术和职业生涯奠定坚实的基础。

 

# 参考文献
[1]苏桐.Docker镜像安全监测模型研究与设计[J].计算机时代,2023(06):25-28.DOI:10.16644/j.cnki.cn33-1094/tp.2023.06.006.

[2]刘小磊,程伟华,章路进.基于Prometheus的云计算资源全链路监控系统[J].电子设计工程,2023,31(02):170-174.DOI:10.14022/j.issn1674-6236.2023.02.036.

[3]栗晓晗,张新有.基于系统调用的Docker容器异常检测[J].微电子学与计算机,2022,39(12):77-85.DOI:10.19304/J.ISSN1000-7180.2022.0205.

[4]谢超群.基于Prometheus的容器云监控平台应用研究[J].西安文理学院学报(自然科学版),2022,25(04):23-26.

[5]张晓强.基于Prometheus体系的复杂业务系统监控实践[J].新型工业化,2022,12(07):261-264.DOI:10.19335/j.cnki.2095-6649.2022.7.060.

[6]谢兆贤,倪冰雪,王若冰.基于异常检测Docker容器的监控系统研究[J].计算机技术与发展,2022,32(06):131-137.

[7]郭建磊,车学董,王宁宁.基于Prometheus的工业云平台监控告警服务系统[J].信息技术与信息化,2021(12):142-145.

[8]李南华.基于Docker容器的云资源监控设计与实现[J].电子世界,2021(18):130-132.DOI:10.19353/j.cnki.dzsj.2021.18.054.

[9]王召选.基于Prometheus的GPU服务器运维监控系统[J].信息与电脑(理论版),2021,33(09):131-133.

[10]朱立松. 基于Prometheus的高可用告警监控系统设计与实现[D].华中科技大学,2020.DOI:10.27157/d.cnki.ghzku.2020.007079.

[11]吴昊. 基于Docker的业务监控和告警系统的设计与实现[D].华中科技大学,2020.DOI:10.27157/d.cnki.ghzku.2020.006723.

[12]黄静,陈秋燕.基于Prometheus + Grafana实现企业园区信息化PaaS平台监控[J].数字通信世界,2020(09):70-72.
