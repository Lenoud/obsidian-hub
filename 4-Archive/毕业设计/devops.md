System information

System:

Current User:   git

Using RVM:      no

Ruby Version:   2.4.4p296

Gem Version:    2.7.6

Bundler Version:1.16.2

Rake Version:   12.3.1

Redis Version:  3.2.11

Git Version:    2.17.1

Sidekiq Version:5.1.3

Go Version:     unknown

GitLab information

Version:        11.1.4

Revision:       63daf37

Directory:      /opt/gitlab/embedded/service/gitlab-rails

DB Adapter:     postgresql

URL:            [http://gitlab-75c5bf598d-hm7jt](http://gitlab-75c5bf598d-hm7jt)

HTTP Clone URL: [http://gitlab-75c5bf598d-hm7jt/some-group/some-project.git](http://gitlab-75c5bf598d-hm7jt/some-group/some-project.git)

SSH Clone URL:  git@gitlab-75c5bf598d-hm7jt:some-group/some-project.git

Using LDAP:     no

Using Omniauth: no

GitLab Shell

Version:        7.1.4

Repository storage paths:

- default:      /var/opt/gitlab/git-data/repositories

Hooks:          /opt/gitlab/embedded/service/gitlab-shell/hooks

Git:            /opt/gitlab/embedded/bin/git

目录

[一、设计背景](#_Toc148515797)

[1.1项目简介](#_Toc148515798)

[1.1.1 项目介绍](#_Toc148515799)

[1.1.2 cicd理念](#_Toc148515800)

[1.2课题目标](#_Toc148515801)

[二、设计思路](#_Toc148515802)

[2.1开发环境与工具](#_Toc148515803)

[2.1.1 ESXI](#_Toc148515804)

[2.1.2 kubeeasy](#_Toc148515805)

[2.1.3 MobaXterm](#_Toc148515806)

[2.1.4 git](#_Toc148515807)

[2.1.5 Harbor](#_Toc148515808)

[2.1.6 其他](#_Toc148515809)

[2.2项目结构](#_Toc148515810)

[2.2.1 kubernetes架构](#_Toc148515811)

[2.2.2 gitlab-ci](#_Toc148515812)

[2.2.3 gitlab](#_Toc148515813)

[三、需求分析](#_Toc148515814)

[3.1 功能需求分析](#_Toc148515815)

[3.2 非功能需求分析](#_Toc148515816)

[3.3 用户需求分析](#_Toc148515817)

[四、系统设计](#_Toc148515818)

[4.1 架构设计](#_Toc148515819)

[4.2 数据模型设计](#_Toc148515820)

[4.3 安全设计](#_Toc148515821)

[4.4 组件设计](#_Toc148515822)

[4.4.1 k8s组件](#_Toc148515823)

[4.4.2 harbor组件](#_Toc148515824)

[4.4.3 gitlab相关组件](#_Toc148515825)

[五、系统实现](#_Toc148515826)

[5.1 kubernetes部署](#_Toc148515827)

[5.1.1 服务器环境设置](#_Toc148515828)

[5.1.2 IP地址规划](#_Toc148515829)

[5.1.3 安装部署工具](#_Toc148515830)

[5.1.4 集群安装依赖包](#_Toc148515831)

[5.1.5 基础ssh配置](#_Toc148515832)

[5.1.6 集群升级系统内核](#_Toc148515833)

[5.1.7 部署kubernetes集群](#_Toc148515834)

[5.1.8 浏览器访问kuboard](#_Toc148515835)

[5.2 harbor私有仓库部署](#_Toc148515836)

[5.2.1 harbor主机环境准备](#_Toc148515837)

[5.2.2 安装docker和docker-compose](#_Toc148515838)

[5.2.3 部署harbor](#_Toc148515839)

[5.2.4 将harbor注册到systemd管理](#_Toc148515840)

[5.2.5 浏览器访问Harbor](#_Toc148515841)

[5.3 Gitlab平台搭建](#_Toc148515842)

[5.3.1 基础准备](#_Toc148515843)

[5.3.2 部署Gitlab服务](#_Toc148515844)

[5.3.3 配置Gitlab平台](#_Toc148515845)

[5.4 部署Gitlab CI Runner](#_Toc148515846)

[5.4.1 获取 Gitlab CI Register Token](#_Toc148515847)

[5.4.2 编写Gitlab CI Runner资源清单文件](#_Toc148515848)

[5.5 开始编写CI项目](#_Toc148515849)

[5.5.1 创建一个新项目](#_Toc148515850)

[5.5.2 开始编写代码](#_Toc148515851)

[5.5.3 编写流水线脚本触发CI自动部署](#_Toc148515852)

[5.5.4  查看构建情况](#_Toc148515853)

[5.5.5 访问wordpress](#_Toc148515854)

[六、总结](#_Toc148515855)

[参考文献](#_Toc148515856)

# 一、设计背景
## 1.1项目简介
### 1.1.1 项目介绍
这个项目旨在利用当前非常流行的开源容器平台Kubernetes和代码托管平台GitLab与harbor私有镜像仓库，解决企业多人团队开发中的繁琐和复杂的管理挑战。它通过简化团队成员和代码管理，同时简化运维人员的部署和上线流程，大幅提升了业务开发和上线速度。此外，项目提供了用户友好的Kubernetes和GitLab与harbor的Web界面，用于管理对应平台的服务，使其更易用，适用于广大用户。

### 1.1.2 cicd理念
CI 持续集成（Continuous Integration）协同开发是一种主流的开发方式，允许多名开发人员同时处理同一个应用的不同模块或功能。然而，当企业计划在同一天将所有开发分支的代码合并到主分支时，可能会遇到一些挑战，导致时间和努力的浪费，因为代码冲突难以避免。如果开发人员的本地环境与线上环境不一致，问题会变得更加复杂。为了解决这些问题，持续集成（CI）变得至关重要。CI允许开发者更轻松地将代码更改合并到主分支。一旦开发人员将代码更改合并到主分支，系统会自动构建应用程序并运行各种自动化测试，通常包括单元测试和集成测试，以验证更改没有对应用程序造成破坏。如果自动化测试发现新代码与现有代码存在冲突，CI可以更轻松地快速修复这些错误。这样，CI确保了代码的高质量和稳定性，减少了手动解决问题的工作量。CD 持续交付（Continuous Delivery）持续交付（Continuous Delivery）是一种自动化流程，它在完成构建、单元测试和集成测试等自动化步骤后，可以自动将经过验证的代码部署到企业的代码库。这意味着持续交付旨在建立一个可随时将开发环境的功能部署到生产环境的代码库的机制。在持续交付过程中，每个步骤都包括测试自动化和代码发布自动化。这使得运维团队能够在流程结束时，轻松快速地将应用程序部署到生产环境中。CD 持续部署（Continuous Deployment）对于一个完整、成熟的CI/CD管道来说，最后的阶段是持续部署，它是持续交付的延伸，可以自动将应用程序发布到生产环境。持续部署的核心特点是，一旦开发人员完成对应用程序的更改并通过了自动化测试，这些更改将在几分钟内自动生效，从而让运维团队能够持续接收用户反馈并进行集成。综上所述，CI/CD的各个关联步骤共同降低了应用程序的部署风险。然而，需要指出的是，为适应CI/CD管道中的各种测试和发布阶段，需要编写大量的自动化测试，这在前期工作上可能会耗费较多的时间和资源。但这些投入最终会为提高开发速度、质量和可靠性带来显著的回报。

## 1.2课题目标
本课题主要考验学生对虚拟化服务器的使用和对kubernetes容器平台的掌握，以及对devops流程的理解，我们首先需要创建两台虚拟机或云主机（分别命名为master和worker）并使用虚拟化服务（如VMware、ESXi、OpenStack、KVM等）创建虚拟机，部署Kubernetes平台。平台部署完成后，我们需要在master节点上部署NFS服务，并导入必要的Docker镜像。接着，使用Kubernetes Dashboard平台，编写相应的YAML文件来创建以下资源：Service网络、PersistentVolume卷、Deployment资源以及Namespace名称空间来部署GitLab，此处需要对kubernets和docker容器平台有一定了解，培养学生对容器化服务的概念。完成这些步骤后，我们将登录GitLab平台，进行相关配置，包括创建项目、推送项目代码等。然后，我们将继续编写YAML文件以部署GitLab Runner服务，确保ConfigMap资源传递必要的环境变量、StatefulSet资源在每个节点上创建Pod，并创建ServiceAccount。我们还需要创建一个RBAC资源清单文件。完成上述步骤后，我们将Kubernetes集群添加到GitLab项目中，并编写流水线脚本，实现项目的自动化发布。这个流程将确保项目的持续集成和持续交付，提高开发效率和可靠性。

# 二、设计思路
## 2.1开发环境与工具
本次部署全程为离线环境，所需要的软件包和工具已经准备好，并且上传虚拟机。

表 2-1 使用的工具

| 工具 | 作用 |
| :---: | :---: |
| ESXI server | 主要用于创建虚拟机 |
| MobaXterm | 连接和管理虚拟机所需要的工具 |
| kubeeasy | 搭建k8s的命令工具 |
| Git | 软件开发版本控制系统 |
| harbor | 私有镜像仓库 |

 

### 2.1.1 ESXI
ESXi是VMware公司开发的一款虚拟化操作系统，它用于创建和管理虚拟化环境。ESXi是一种虚拟化技术，它允许在一台物理服务器上同时运行多个虚拟机（VM）。每个虚拟机可以独立运行不同的操作系统和应用程序，而它们共享物理服务器的计算资源。这种虚拟化技术使得数据中心和服务器资源的管理更加灵活和高效，同时提高了硬件资源的利用率。ESXi是VMware虚拟化解决方案的一部分，广泛用于企业和数据中心环境中，以实现服务器的虚拟化和资源隔离。

### 2.1.2 kubeeasy
Kubeeasy是一个开源项目，位于GitLab上，它旨在提供一个快速且简洁的方式来部署Kubernetes（k8s）集群。它支持以下功能：快速部署：Kubeeasy命令工具可以迅速部署一个Kubernetes集群，使得集群搭建变得简单和高效。内核升级：该工具支持集群内核的升级，升级后的内核版本为5.17。离线部署：Kubeeasy允许离线部署普通的Kubernetes集群，K8S版本为v1.21.3。高可用集群：除了普通集群，Kubeeasy还支持离线部署高可用（HA）的Kubernetes集群，K8S版本同样为v1.21.3。这些HA集群使用了云原生的kube-vip，负载均衡功能。图形化管理工具：Kubeeasy默认安装了图形化管理工具kuboard-v2，使得集群的管理更加直观和便捷。存储类：Kubeeasy默认安装了local-hostpath存储类，以满足持久化存储的需求。长期证书：Kubeeasy为Kubernetes集群提供了长达100年的证书，并启用了自动轮询功能，确保证书的有效性。容器镜像功能：Kubeeasy内置了一系列容器镜像功能，包括拉取（pull）、推送（push）、保存（save）、加载（load），以方便镜像的管理和使用。

### 2.1.3 MobaXterm
MobaXterm是一款功能强大的跨平台远程计算机管理工具，旨在为系统管理员、开发人员和网络工程师提供方便的远程访问和系统管理功能，MobaXterm包括了SFTP和SCP客户端，使文件传输和管理变得简单。你可以轻松地上传和下载文件，甚至可以在不同主机之间复制文件，而且它可作为便携式应用程序运行，无需安装，可在不同计算机上使用，也支持将会话和设置导出和导入。

### 2.1.4 git
Git是一个分布式版本控制系统，用于跟踪和管理源代码的变化。它允许开发者记录、比较和恢复项目的不同版本。Git具有分支管理、远程协作、高效性能等特点，使团队能够更轻松地协同开发和管理代码。 Git还支持标签和版本的管理，使项目版本控制更加灵活和可靠。

### 2.1.5 Harbor
Harbor是由VMware公司开源的一个企业级Docker Registry管理项目。它提供了一系列强大的功能，包括权限管理（RBAC）、LDAP集成、日志审计、用户友好的管理界面、自助注册、镜像复制以及中文语言支持等。这些功能使Harbor成为一个完备的容器镜像管理解决方案，适用于企业和组织，能够满足他们对容器镜像的安全性和可管理性的需求。

### 2.1.6 其他
表 2-2 系统版本

| **System** | **Version** |
| :---: | :---: |
| ESXI | 7.0 |
| harbor | 2.5.3 |
| kubernetes | 1.21.3 |
| CentOS | 7.9.2009 |
| git | 1.8.3.1 |

 

## 2.2项目结构
### 2.2.1 kubernetes架构
Kubernetes的前身是谷歌的Borg系统。Borg是谷歌内部的大规模集群管理系统，其主要任务是对谷歌的核心服务进行调度和管理。Borg的核心目标是为用户提供资源管理的抽象层，使他们可以专注于自身的核心业务，而无需担心底层资源的管理问题。同时，Borg旨在实现跨多个数据中心的资源利用率最大化。Kubernetes在设计上汲取了Borg的许多设计理念和概念，例如Pod、Service、Labels以及单Pod单IP等。这些概念和设计原则有助于Kubernetes实现类似Borg的资源管理和调度功能。总体而言，Kubernetes的整体架构与Borg非常相似，这使得Kubernetes成为了一个强大而受欢迎的容器编排平台，有助于简化应用程序的部署和管理。

**   ** ![[1698990001505-3bfc26f4-0b1b-4f2e-b0c3-54e7cd6a6ba2.png]]

图 2-1 kubernetes架构图

### 2.2.2 gitlab-ci
GitLab CI/CD（持续集成/持续部署）是GitLab平台的一部分，用于自动化和优化软件构建、测试和部署过程。它允许以代码的方式定义和管理CI/CD流程，从而更轻松地维护和扩展我们的开发工作流程。以下是GitLab CI的关键组件和概念：Runner（运行器）：Runner是一个轻量级代理，用于运行在CI/CD流程中定义的任务（jobs）。GitLab Runner可以托管在您的基础架构上（自托管），也可以从共享的GitLab Runner池中使用（共享Runner）。Runner在隔离的环境中执行任务。Pipeline（流水线）：流水线是一系列相互连接的任务，用于定义CI/CD流程。它在将代码推送到GitLab存储库时自动触发，但也可以手动触发。流水线定义了代码从版本控制到部署的整个流程。Job（任务）：任务是流水线的基本构建块，代表了要执行的单个工作单元。每个任务都可以包括构建、测试、部署或其他自定义操作。YAML配置文件：GitLab CI/CD的配置文件是一个YAML格式的文件，用于定义流水线中的Runner和任务。该文件位于Git存储库的根目录中，通常命名为.gitlab-ci.yml。触发器（Triggers）：触发器是用于手动触发流水线的方式，通常用于外部系统或脚本与GitLab CI/CD集成。Artifact（构建产物）：构建产物是任务执行后生成的文件或数据，可以在流水线中的不同任务之间共享，或者用于部署和存档。Runner标签（Tags）：Runner标签是用于将特定任务分配给特定Runner的标识。标签可以用于控制任务的分发和执行。缓存（Caching）：缓存允许我们在任务之间共享文件或数据，以提高流水线的执行效率。环境（Environments）：环境是用于部署和管理应用程序的目标环境，例如开发、测试或生产环境。GitLab CI/CD可以与环境集成，以自动化部署过程。自动部署（Auto DevOps）：GitLab还提供了一种名为Auto DevOps的功能，它是一套预定义的CI/CD模板和自动化规则，可帮助简化部署过程。

![[1698990002161-9a61d186-40d9-42df-9c08-1c9cf093538b.png]]

图 2-2 gitlab-ci 流程图

### 2.2.3 gitlab
GitLab是一个基于Web的开源平台，用于版本控制和协作软件开发。它提供了一套功能强大的工具，以帮助开发团队协同开发、管理代码、进行CI/CD（持续集成/持续部署）以及跟踪问题和项目。以下是GitLab的一些关键特性和概念：版本控制：GitLab支持Git版本控制系统，允许团队在一个集中的平台上协作开发，并有效地管理代码。存储库（Repository）：GitLab存储库是用于存储和管理源代码的地方。每个项目都有一个关联的Git存储库。问题跟踪（Issue Tracking）：GitLab提供了一个问题跟踪系统，允许您创建、分配和跟踪问题、错误和任务。合并请求（Merge Requests）：合并请求是用于将代码更改合并到主分支的机制。它允许其他开发人员审查和讨论代码更改，确保质量和一致性。CI/CD（持续集成/持续部署）：GitLab提供了内置的CI/CD功能，允许自动化构建、测试和部署流程。您可以使用.gitlab-ci.yml文件来定义CI/CD流水线。容器注册表（Container Registry）：GitLab包括一个内置的容器注册表，用于存储和管理Docker镜像，方便部署和交付应用程序。自动化部署（Auto DevOps）：Auto DevOps是GitLab的一项功能，它提供了一套自动化CI/CD模板和最佳实践，可以帮助简化部署过程。权限管理：GitLab提供了灵活的权限管理系统，允许您定义不同用户和组织的访问权限和角色。集成：GitLab可以与各种第三方工具和服务集成，包括Slack、JIRA、Kubernetes等，以便更好地支持团队的工作流程。社区和企业版：GitLab有社区版和企业版。社区版是免费的开源版本，而企业版则提供了更多高级功能和支持选项。

# 三、需求分析
## 3.1 功能需求分析
一键式部署：提供一个简便的操作界面，实现一键式部署应用程序到Kubernetes集群，减少人工操作和错误。

持续集成：支持自动化构建、测试和发布流程，通过集成各种工具和流程，确保代码质量和稳定性。

版本控制：集成常用的版本控制系统（如Git），实现代码管理、版本比较和协作功能。

监控与告警：实时监控应用程序的运行状态、性能指标和异常情况，并及时发送告警通知，帮助用户快速发现和解决问题。

日志管理：集中管理应用程序的日志，提供快速检索、分析和跟踪功能，便于故障排查和性能优化。

可视化界面：提供友好的可视化界面，方便用户配置和操作CICD流程，支持实时报告和仪表盘展示。

扩展性与灵活性：支持自定义流程和插件，满足不同项目的特殊需求，例如自定义构建步骤、测试框架和发布策略等。

## 3.2 非功能需求分析
性能：CICD平台需要具备高效稳定的性能，能够快速处理大量的构建、测试和发布任务，并保持系统的响应速度。

安全性：保护代码和敏感信息的安全性，确保只有授权用户能够访问和操作CICD平台，同时提供数据加密和访问控制机制。

可靠性：CICD平台需要具备高可用性和容错能力，能够自动恢复故障，防止单点故障导致整个流程中断。

可维护性：CICD平台应该易于部署、配置和维护，提供良好的文档和日志记录，以便系统管理员进行日常管理和故障排除。

易用性：用户界面应该友好、操作简单，降低学习和使用成本，同时提供适当的帮助文档和培训资源。

## 3.3 用户需求分析
快速部署和更新应用程序；平台能够快速且自动地部署和更新应用程序，以便更高效地进行开发和发布。

自动化构建、测试和发布流程；平台能够建立自动化的构建、测试和发布流程，以减轻重复性工作的负担，提高交付速度和质量。

可靠的版本控制和协作工具；需要一个可靠的版本控制系统，使得多人协同工作时能够方便地管理和合并代码。

实时监控和日志记录；平台能够实时监控应用程序的运行状态和性能指标，并记录相关日志，以便及时发现和处理问题。

可视化的界面与报告；平台提供一个可视化的界面，以便直观地查看和管理CICD平台的各项功能和报告。

高度可扩展和灵活性； CICD平台具有高度可扩展性和灵活性，能够满足不同项目和团队的特殊需求。

# 四、系统设计
## 4.1 架构设计
GitLab CI 的部署架构：确定 GitLab CI 的分布式部署方式，包括 Runner 节点的规划和部署、GitLab 服务器的部署、GitLab Runner 的注册和管理等。

高可用性和可扩展性：确保 GitLab CI 系统具备高可用性和可扩展性，可以满足不同规模和负载的需求。可以考虑使用负载均衡和集群架构来实现高可用性和水平扩展能力。

安全设计：考虑如何保护 GitLab CI 系统的安全性，包括用户认证和授权机制、敏感数据的加密和传输安全、访问控制等措施。

## 4.2 数据模型设计
项目和仓库的数据模型：定义项目和仓库相关的数据对象，包括项目信息、仓库信息、分支、标签等。

流水线和任务的数据模型：定义流水线和任务的数据对象，包括流水线的状态、任务的执行记录、执行日志等。

数据库设计和性能优化：确定选择合适的数据库类型（如关系型数据库或 NoSQL 数据库），设计数据库表结构，并进行性能优化，确保系统具备良好的性能和扩展性。

## 4.3 安全设计
用户认证和授权：设定合适的用户认证和授权机制，确保只有授权用户可以访问和操作 GitLab CI 系统。

敏感数据的保护：对于敏感数据（如凭据、密钥等），采用合适的加密算法进行加密，并确保安全传输和存储。

漏洞和安全监测：建立漏洞扫描和安全监测机制，及时发现和处理潜在的安全风险和威胁。

## 4.4 组件设计
### 4.4.1 k8s组件
kube-proxy：负责在集群内部实现网络代理和负载均衡功能。

kubelet：负责管理每个节点上的容器，与Master节点通信，确保容器按照规定的状态运行。

kube-controller-manager：维护集群中各个资源的状态，例如副本集、服务等。

kube-scheduler：负责根据资源需求和约束条件，将Pod分配到适合的节点上运行。

![[1698990002702-e051847c-dfc6-4ce5-9032-b734b582bedb.png]]

图4-1 k8s组件

### 4.4.2 harbor组件
harbor-core：核心组件，包括认证、授权、镜像存储等功能。

harbor-jobservice：负责处理Harbor的后台任务，例如垃圾回收、镜像复制等。

harbor-ui：提供用户界面，包括用户管理、项目管理、镜像仓库的管理等功能。

harbor-registry：用于存储和管理镜像，支持镜像的推送、拉取、复制等操作。

![[1698990003101-e22f922d-4fb2-4e6a-8622-8a4cb8900bf1.png]]

图4-2 harbor组件

### 4.4.3 gitlab相关组件
gitlab-shell：处理Git命令的请求，并与GitLab进行交互。

gitlab-workhorse：处理GitLab的HTTP请求，提供高性能的代理和缓存功能。

gitlab-web：提供Web界面，包括代码管理、CI/CD管道、问题跟踪等功能。

gitlab-runner：负责执行CI/CD管道中的任务，支持多种执行器，如shell、Docker等。

![[1698990003444-138ff858-2d5a-4f74-bfcd-86cebe8afb7f.png]]

图4-3  gitlab组件

# 五、系统实现
## 5.1 kubernetes部署
### 5.1.1 服务器环境设置
每台服务器的系统环境、角色、物理配置信息，如表5-1所示。

表 5-1 服务器配置详情

| **角色** | **系统架构** | **内存大小** | **核心数量** |
| :---: | :---: | :---: | :---: |
| master | centos7.2009 x64 | 6G | 6cpu |
| worker | centos7.2009 x64 | 6G | 6cpu |
| Harbor | centos7.2009 x64 | 2G | 1cpu |

认证：集群节点需统一认证，需使用同一用户名和密码，本次项目使用root用户密码为000000，实际生产环境应考虑安全方面相关问题。

### 5.1.2 IP地址规划
每台服务器的IP地址规划、主机名、信息如表所示。

表 5-2 ip地址规划

| **IP** **地址** | **主机名** | **角色** |
| :---: | :---: | :---: |
| 192.168.100.100 | k8s-maste-noder01 | master | etcd | worker |
| 192.168.100.110 | k8s-worker-node1 | worker |
| 192.168.100.120 | harbor | registry |

 

### 5.1.3 安装部署工具
此次实验为离线安装，所需的软件包均已打包，直接使用ftp传入虚拟机。将离线安装包传输到虚拟机中。

#master节点

hostnamectl set-hostname k8s-maste-noder01

#修改主机名称

mount /root/kubernetes-v1.21.3_kubeeasy-v1.31.iso /mnt 

#挂载安装k8s所需要的软件包

cp -rvf /mnt/* /opt

#拷贝软件包到opt目录

mv /opt/kubeeasy1.3.1/kubeeasy-v1.3.1 /usr/local/bin/kubeeasy 

#将命令工具移动到PATH目录

### 5.1.4 集群安装依赖包
使用kubeeasy在集群中安装软件包，包含远程连接ssh pass等软件包。

#master节点执行，

cd /opt/kubeeasy1.3.1/

kubeeasy install depend \
--host 192.168.100.100,192.168.100.110 \
--user root \
--password 000000 \
--offline-file ./centos-7-rpms.tar.gz

#hots 为k8s集群的节点IP，user为执行安装命令的用户，password为user的登录密码，offline-file指定软件包目录

![[1698990003865-169c6ac0-3c12-40ff-bce7-3418d1b50d1e.png]]

图 5-1 集群依赖包安装

### 5.1.5 基础ssh配置
配置节点之间的免密登录。

#master节点执行

#检查节点是否能通信，

kubeeasy check ssh \
--host 192.168.100.100,192.168.100.110 \
--user root \
--password 000000

#配置免密登录，

kubeeasy create ssh-keygen \
--master 192.168.100.100 \
--worker 192.168.100.110 \
--user root \
--password 000000

#master节点连接node节点，测试是否免密成功，

ssh 192.168.100.110

![[1698990004156-2960ece4-a511-45e8-9124-20ff623ab361.png]]

图 5-2 检查节点连通性操作

![[1698990004400-4ba8e6e9-9e82-432f-b5e7-940ddaeaf3ad.png]]

图 5-3 节点免密操作

![[1698990004947-74eaa8dc-4b36-479f-bad5-2c1386c0b623.png]]

图 5-4 节点免密测试

### 5.1.6 集群升级系统内核
使用kubeeasy升级集群的内核，将其3.10的内核升级为5.17（建议升级内核）。

脚本执行完后，会自动重启主机，以生效系统内核。

#master节点执行，

kubeeasy install upgrade-kernel \
--host 192.168.100.100,192.168.100.110 \
--user root \
--password 000000 \
--offline-file ./kernel-rpms-v5.17.2.tar.gz

![[1698990005264-d09708e1-108f-4b04-a788-1f6c06951d6b.png]]

图 5-5 内核升级

### 5.1.7 部署kubernetes集群
使用kubeeasy部署kubernetes集群，以下分为**普通集群**和**高可用集群**的安装方式，根据实际情况选择一种方案部署。安装后的K8S的版本为v1.21.3，使用docker作为容器运行时，网络组件使用calico网络，默认还安装kuboard图形化管理器和K8S存储类（openebs-hostpath），K8S的证书有效期为100年，且开启了证书轮换，本次安装普通集群模式。

#master节点执行,。

cd /opt/kubeeasy1.3.1     #如需修改k8s的pod网段，修改—pod-cidr参数相应配置。

kubeeasy install kubernetes \
   --master 192.168.100.100 \
   --worker 192.168.100.110 \
   --user root \
   --password 000000 \
   --version 1.21.3 \
   --pod-cidr 10.244.0.0/16 \
   --offline-file ./kubeeasy-v1.3.1.tar.gz

![[1698990005740-f29edace-4811-4a24-9b54-95edd4e512d3.png]]

图 5-6 部署kubernetes集群

### 5.1.8 浏览器访问kuboard
查询仪表盘的端口号，通过浏览器访问web页面。

#master节点执行

#查看kuboard开放的端口号

[root@k8s-master-node1 ~]# kubectl get svc -n kube-system kuboard

NAME      TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE

kuboard   NodePort   10.96.120.99   <none>        80:32567/TCP   7m25s

#浏览器访问192.168.100.100:32567。

cat /root/k8s-token.txt

#获取token登录平台。

![[1698990006121-a28fe36b-95ed-4283-b581-6deff61a17df.png]]

图 5-7  kuboard登录页面

![[1698990006480-dc295dcb-e0e1-4359-9478-426af62c9178.png]]

图 5-8  kuboard管理页面

## 5.2 harbor私有仓库部署
### 5.2.1 harbor主机环境准备
Harbor服务器关闭防火墙。

#harbor节点执行。

hostnamectl set-hostname harbor #修改主机名

systemctl stop firewalld &&systemctl disable firewalld #关闭防火墙

sed -i "s/SELINUX=.*/SELINUX=disabled/g" /etc/selinux/config

setenforce 0 && getenforce  #关闭selinux

### 5.2.2 安装docker和docker-compose
部署docker服务用于启动harbor私有仓库。

#harbor节点执行。

tar -zxvf docker-20.10.10.tgz -C /opt/  #解压docker离线包

cp -rvf /opt/docker/* /usr/bin/  #拷贝docker可执行文件到PATH

chmod 777 docker-compose-linux-x86_64  #赋予Docker-compose二进制文件可执行权限

mv docker-compose-linux-x86_64 /usr/local/bin/docker-compose #安装docker-compose

#注册docker到systemd。

cat >> /etc/systemd/system/docker.service <<EOF

[Unit]

Description=Docker Application Container Engine

Documentation=https://docs.docker.com

After=network-online.target firewalld.service

Wants=network-online.target

 

[Service]

Type=notify

# the default is not to use systemd for cgroups because the delegate issues still

# exists and systemd currently does not support the cgroup feature set required

# for containers run by docker

ExecStart=/usr/bin/dockerd

ExecReload=/bin/kill -s HUP $MAINPID

# Having non-zero Limit*s causes performance problems due to accounting overhead

# in the kernel. We recommend using cgroups to do container-local accounting.

LimitNOFILE=infinity

LimitNPROC=infinity

LimitCORE=infinity

# Uncomment TasksMax if your systemd version supports it.

# Only systemd 226 and above support this version.

#TasksMax=infinity

TimeoutStartSec=0

# set delegate yes so that systemd does not reset the cgroups of docker containers

Delegate=yes

# kill only the docker process, not all processes in the cgroup

KillMode=process

# restart the docker process if it exits prematurely

Restart=on-failure

StartLimitBurst=3

StartLimitInterval=60s

 

[Install]

WantedBy=multi-user.target

EOF

#开启私有仓库权限

mkdir /etc/docker/

cat >> /etc/docker/daemon.json <<EOF

{ 

"insecure-registries": ["0.0.0.0/0"],

  "registry-mirrors": ["https://fem5eo07.mirror.aliyuncs.com"]

}

#开启docker

systemctl daemon-reload  #重新加载systemd的配置文件

systemctl enable docker.service  #docker开机自启

systemctl start docker #启动docker

![[1698990006875-ccd58970-2bfe-4180-bc64-10d84ac72c6a.png]]

图 5-9 配置docker开机自启

### 5.2.3 部署harbor
解压harbor的离线安装包，修改相关配置文件，使用脚本安装。

#harbor节点执行。

tar -zxvf harbor-offline-installer-v2.5.3.tgz -C /opt #解压harbor离线包

cd /opt/harbor

cat harbor.yml.tmpl |grep ^[^#]  > harbor.yml #配置harbor.yml

cat harbor.yml |grep -Ev "#"

hostname: 192.168.100.120  #此处填写harborIP

http:

  port: 80

harbor_admin_password: Harbor12345  #harbor密码

database:

  password: root123

  max_idle_conns: 100

  max_open_conns: 900

data_volume: /data

trivy:

  ignore_unfixed: false

  skip_update: false

  offline_scan: false

  insecure: false

jobservice:

  max_job_workers: 10

notification:

  webhook_job_max_retry: 10

chart:

  absolute_url: disabled

log:

  level: info

  local:

    rotate_count: 50

    rotate_size: 200M

    location: /var/log/harbor

_version: 2.5.0

proxy:

  http_proxy:

  https_proxy:

  no_proxy:

  components:

    - core

    - jobservice

    - trivy

upload_purging:

  enabled: true

  age: 168h

  interval: 24h

  dryrun: false

./install.sh   #执行harbor安装脚本

![[1698990007172-b2f9b0e2-4caa-4249-bb4e-bc4f60632675.png]]

图5-10 harbor安装

### 5.2.4 将harbor注册到systemd管理
Harbor默认重启后不会自动启动，所以我们可以将它注册到systemd可以很方便的进行管理。

#harbor节点执行

#编写systemd配置文件

cat >> /etc/systemd/system/harbor.service << EOF

[Unit]

Description=Harbor

After=docker.service systemd-networkd.service systemd-resolved.service

Requires=docker.service

Documentation=http://github.com/vmware/harbor

 

[Service]

Type=simple

Restart=on-failure

RestartSec=5

ExecStart=/usr/local/bin/docker-compose -f /opt/harbor/docker-compose.yml up

ExecStop=/usr/local/bin/docker-compose -f /opt/harbor/docker-compose.yml down

#/usr/local/bin/docker-compose  docker-compose地址

[Install]

WantedBy=multi-user.target

EOF

#设置开机自启

systemctl daemon-reload && systemctl enable harbor

### 5.2.5 浏览器访问Harbor
浏览器输入harbor主机ip即可访问harbor登录页面。

![[1698990007471-02ffcdb4-34ed-422a-be00-71d95743b930.png]]

图 5-11 Harbor登录界面

账号：admin

密码：Harbor12345

登录harbor：

![[1698990007783-4e82b6c9-b30e-48b7-8519-0b33403d6a19.png]]

图 5-12 Harbor管理界面

## 5.3 Gitlab平台搭建
### 5.3.1 基础准备
Master节点部署nfs,为后续创建pv持久化存储卷做支持，先登录harbor仓库创建一个gitlab的公开项目，然后导入gitlab相关镜像到harbor仓库。

#master节点

echo 192.168.100.120 hub.harbor.com >>  /etc/hosts #配置解析

ssh root@k8s-worker-node1 "echo 192.168.100.120 hub.harbor.com >>  /etc/hosts"

docker login hub.harbor.com  #登录私有仓库，

docker load -i gitlab-ce_v1.0.tar.gz  #导入镜像到本地

docker tag gitlab-ce:v1.0  hub.harbor.com/gitlab/gitlab-ce:v1.0 #打标签

docker push hub.harbor.com/gitlab/gitlab-ce:v1.0 #推送镜像到harbor

docker load -i gitlab-runner_v1.0.tar.gz #导入镜像到本地

docker tag gitlab-runner:v1.0  hub.harbor.com/gitlab/gitlab-runner:v1.0 #打标签

docker push hub.harbor.com/gitlab/gitlab-runner:v1.0 #推送镜像到harbor

#配置本地yum源

rm -rf /etc/yum.repos.d/

cat >> local.repo << EOF

[local]

name=centos7

baseurl=file:///opt/centos/

gpgcheck=0

enabled=1

EOF

yum -y install git nfs-utils  #安装nfs和git

systemctl start nfs #开机自启

systemctl enable nfs #开机自启

#配置nfs

echo "/root/nfs/gitlab-conf 192.168.100.0/24(insecure,rw,sync,no_root_squash)" >> /etc/exports

echo "/root/nfs/gitlab-log 192.168.100.0/24(insecure,rw,sync,no_root_squash)" >> /etc/exports

echo "/root/nfs/gitlab-data 192.168.100.0/24(insecure,rw,sync,no_root_squash)" >> /etc/exports

exportfs -r  && exportfs  #生效配置。

![[1698990008132-0ff1155d-f31b-4f54-b5e6-8e84f5bfdd87.png]]

图 5-13 登录harbor

![[1698990008413-c885907f-6c1a-4568-a139-f423bab1d9be.png]]

图 5-14 nfs配置

查看harbor仓库中镜像是否上传成功。

![[1698990008719-3043e9f5-862d-4027-8195-a588a45da861.png]]

图 5-15 harbor镜像查看

### 5.3.2 部署Gitlab服务
#### 5.3.2.1 创建持久化存储卷
Kubernetes有多种方式创建资源，我们一般常用编写yaml文件的形式启动相对应的资源，方便我们后续维护、查看我们创建的资源，在k8s中启动一个pod当它发生故障或者意外删除，它内部的数据就会丢失，这非常不利于我们在生产环境使用，所以我们需要创建pv和pvc使数据持久化存储,本项目演示命令行与web两种方式创建。

#master节点

mkdir gitlab-yaml

cat >> gitlab-yaml/namespace-gitlab.yaml << EOF

apiVersion: v1
kind: Namespace
metadata:
  name: gitlab
EOF

kubectl apply -f namespace-gitlab.yaml # 创建gitlab名称空间

#web方式创建，

#创建存储卷

cat >> gitlab-yaml/pvc-pv-gitlab.yaml <<EOF

---

apiVersion: v1

kind: PersistentVolume

metadata:

  name: gitlab-data

spec:

  accessModes:

  - ReadWriteMany

  capacity:

    storage: 15Gi

  nfs:

    path: /root/nfs/gitlab-data

    server: 192.168.100.100

  persistentVolumeReclaimPolicy: Retain

  storageClassName: openebs-hostpath

  volumeMode: Filesystem

---

apiVersion: v1

kind: PersistentVolume

metadata:

  name: gitlab-conf

spec:

  accessModes:

  - ReadWriteMany

  capacity:

    storage: 2Gi

  nfs:

    path: /root/nfs/gitlab-conf

    server: 192.168.100.100

  persistentVolumeReclaimPolicy: Retain

  storageClassName: openebs-hostpath

  volumeMode: Filesystem

---

apiVersion: v1

kind: PersistentVolume

metadata:

  name: gitlab-log

spec:

  accessModes:

  - ReadWriteMany

  capacity:

    storage: 1Gi

  nfs:

    path: /root/nfs/gitlab-log

    server: 192.168.100.100

  persistentVolumeReclaimPolicy: Retain

  storageClassName: openebs-hostpath

  volumeMode: Filesystem

---

apiVersion: v1

kind: PersistentVolumeClaim

metadata:

  name: gitlab-data

  namespace: gitlab

spec:

  accessModes:

    - ReadWriteMany

  resources:

    requests:

      storage: 15G

  storageClassName: openebs-hostpath

---

apiVersion: v1

kind: PersistentVolumeClaim

metadata:

  name: gitlab-conf

  namespace: gitlab

spec:

  accessModes:

    - ReadWriteMany

  resources:

    requests:

      storage: 2G

  storageClassName: openebs-hostpath

---

apiVersion: v1

kind: PersistentVolumeClaim

metadata:

  name: gitlab-log

  namespace: gitlab

spec:

  accessModes:

    - ReadWriteMany

  resources:

    requests:

      storage: 1G

  storageClassName: openebs-hostpath

EOF

kubectl apply -f gitlab-yaml/pvc-pv-gitlab.yaml #创建pv和pvc资源

#Web界面创建pvc&pv。

![[1698990009154-e55001b8-862e-42cc-baed-57200d1cc3fa.png]]

图 5-16 名称空间创建

 

![[1698990009648-f97433a5-1ce5-49bf-940c-cf0dadd615b0.png]]

图 5-17  pv和pvc持久卷创建

 

#### 5.3.2.2 部署gitlab和service资源
以deployment的方式创建gitlabPod,并创建svc暴露服务,让我们通过masterIP+端口号的形式可以访问到web服务。

#master节点

#创建一个deployment与service

kubectl label nodes k8s-master-node1 app=web

#为master节点打上标签，实现gitlab自动注入到master节点

cat  >> gitlab-yaml/deploy-svc-gitlab.yaml << EOF

apiVersion: apps/v1

kind: Deployment

metadata:

  name: gitlab

  namespace: gitlab

  labels:

name: gitlab

app: web

spec:

  selector:

    matchLabels:

      name: gitlab

  template:

    metadata:

      name: gitlab

      labels:

        name: gitlab

    spec:

      containers:

      - name: gitlab

        image: hub.harbor.com/gitlab/gitlab-ce:v1.0

        imagePullPolicy: IfNotPresent

        env:

        - name: GITLAB_ROOT_PASSWORD

          value: lb123456    #密码

        - name: GITLAB_ROOT_EMAIL

          value: [351719672@qq.com](mailto:351719672@qq.com)  #邮箱账号

        - name: GITLAB_HOST

          value: 192.168.100.100  #masterIP

        - name: GITLAB_PORT

          value: "80"

        - name: GITLAB_SSH_PORT

          value: "22"

        ports:

        - name: http

          containerPort: 80

        - name: ssh

          containerPort: 22

        volumeMounts:

        - mountPath: /var/opt/gitlab

          name: gitlab-data

        - mountPath: /var/log/gitlab

          name: gitlab-log

        - mountPath: /etc/gitlab

          name: gitlab-conf

      volumes:

      - name: gitlab-data

        persistentVolumeClaim:

          claimName: gitlab-data

      - name: gitlab-conf

        persistentVolumeClaim:

          claimName: gitlab-conf

      - name: gitlab-log

        persistentVolumeClaim:

          claimName: gitlab-log

---

apiVersion: v1

kind: Service

metadata:

  name: gitlab

  namespace: gitlab

  labels:

    name: gitlab

spec:

  type: NodePort

  ports:

    - name: http

      port: 80

      targetPort: http

      nodePort: 30880

    - name: ssh

      port: 22

      targetPort: ssh

      nodePort: 32222

  selector:

    name: gitlab

EOF

kubectl apply -f gitlab-yaml/deploy-svc-gitlab.yaml #部署gitlab

#web部署

![[1698990010054-8a89b52c-785c-4c7e-a246-b85211315752.png]]

图 5-18 创建deployment和service

等待两分钟后浏览器访问：master_ip:30880。账号与密码在之前的yaml文件设置了，账号：[351719672@qq.com](mailto:351719672@qq.com)密码：lb123456。

![[1698990010430-567525d9-c6a6-4236-960d-325de6950844.png]]

图 5-19 访问gitlab

#### 5.3.2.3 配置pod中的dns解析
Kubernetes的pod之间通过coredns解析dns,我们需要修改coredns对应的configmap文件配置gitlabPod的解析。

#master节点

#查看gitlab名称和ip

[root@k8s-master-node1 ~]# kubectl get pod -n gitlab -owide

NAME                    READY   STATUS    RESTARTS   AGE   IP               NODE               NOMINATED NODE   READINESS GATES

gitlab-6dffd6df-t6b7f   1/1     Running   0          53m   10.244.236.134   k8s-master-node1   <none>           <none>

#编辑coredns的configmap资源添加解析。

kubectl edit configmap coredns -n kube-system  #添加以下字段

        hosts {

        10.244.236.134 gitlab-6dffd6df-t6b7f

        fallthrough

        }

![[1698990010695-196d08e7-e3fe-4019-a25a-1345f99754d9.png]]

图 5-20  配置dns解析

 

### 5.3.3 配置Gitlab平台
    登录gitlab平台配置允许来自钩子和服务的对本地网络的请求。

![[1698990011152-5767ae86-0ae5-4923-bd43-e3767a4be1df.png]]

图 5-21 允许钩子访问本地网络

## 5.4 部署Gitlab CI Runner
### 5.4.1 获取 Gitlab CI Register Token
登录GitLab管理界面(http://master_ip:30880/admin)，然后点击左侧菜单栏中的Runners：

![[1698990011746-8b63d259-662d-41ca-917a-f30118dbd39c.png]]

图 5-22 runner令牌查询

可以看到该页面中有两个重要参数：Runner URL和Register Token，后期部署Runner时会用到这两个参数。

### 5.4.2 编写Gitlab CI Runner资源清单文件
#### 5.4.2.1 编写一个configmap资源传递runner环境变量
编写一个configmap来设置runner所需要的环境变量。

注意GITLAB_CI_TOKEN、CI_SERVER_URL、KUBERNETES_NAMESPACE参数。

#master节点

cat >> gitlab-yaml/runner/runner-configmap.yaml << EOF

apiVersion: v1

data:

  REGISTER_NON_INTERACTIVE: "true" #修改

  REGISTER_LOCKED: "false"

  METRICS_SERVER: "0.0.0.0:9100"

  GITLAB_CI_TOKEN: "fvJz3F6CqEAX2xXPtMUn"  #平台获取的token

  CI_SERVER_URL: "http://192.168.100.100:30880/"  #添加masterIP+port

  RUNNER_REQUEST_CONCURRENCY: "4" #修改

  RUNNER_EXECUTOR: "kubernetes"

  KUBERNETES_NAMESPACE: "gitlab" #修改

  KUBERNETES_PRIVILEGED: "true"

  KUBERNETES_CPU_LIMIT: "1"

  KUBERNETES_CPU_REQUEST: "500m"

  KUBERNETES_MEMORY_LIMIT: "1Gi"

  KUBERNETES_SERVICE_CPU_LIMIT: "1"

  KUBERNETES_SERVICE_MEMORY_LIMIT: "1Gi"

  KUBERNETES_HELPER_CPU_LIMIT: "500m"

  KUBERNETES_HELPER_MEMORY_LIMIT: "100Mi"

  KUBERNETES_PULL_POLICY: "if-not-present"

  KUBERNETES_TERMINATIONGRACEPERIODSECONDS: "10"

  KUBERNETES_POLL_INTERVAL: "5"

  KUBERNETES_POLL_TIMEOUT: "360"

kind: ConfigMap

metadata:

  labels:

    app: gitlab-ci-runner

  name: gitlab-ci-runner-cm

  namespace: gitlab

EOF

#### 5.4.2.2 编写一个用于注册、运行和取消注册Gitlab CI Runner的小脚本
编写一个用于注册、运行和取消注册Gitlab CI Runner的小脚本。只有当Pod正常通过 Kubernetes（TERM信号）终止时，才会触发转轮取消注册。如果强制终止Pod（SIGKILL信号），Runner将不会注销自身。必须手动完成对这种被杀死的Runner的清理，配置清单文件如下：

#master节点

cat >> runner-scripts-configmap.yaml << EOF

apiVersion: v1
data:
  run.sh: |
    #!/bin/bash
    unregister() {
        kill %1
        echo "Unregistering runner ${RUNNER_NAME} ..."
        /usr/bin/gitlab-ci-multi-runner unregister -t "$(/usr/bin/gitlab-ci-multi-runner list 2>&1 | tail -n1 | awk '{print $4}' | cut -d'=' -f2)" -n ${RUNNER_NAME}
        exit $?
    }
    trap 'unregister' EXIT HUP INT QUIT PIPE TERM
    echo "Registering runner ${RUNNER_NAME} ..."
    /usr/bin/gitlab-ci-multi-runner register -r ${GITLAB_CI_TOKEN}
    sed -i 's/^concurrent.*/concurrent = '"${RUNNER_REQUEST_CONCURRENCY}"'/' /home/gitlab-runner/.gitlab-runner/config.toml
    echo "Starting runner ${RUNNER_NAME} ..."
    /usr/bin/gitlab-ci-multi-runner run -n ${RUNNER_NAME} &
    wait
kind: ConfigMap
metadata:
  labels:
    app: gitlab-ci-runner
  name: gitlab-ci-runner-scripts
  namespace: gitlab

EOF

#### 5.4.2.3 编写用于运行Runner的控制器对象
接下来编写用于运行Runner的控制器对象，这里使用Statefulset资源对象。首先，在开始运行的时候，尝试取消注册所有的同名Runner，然后再尝试重新注册并开始运行。另外通过使用envFrom来指定ConfigMaps来用作环境变量，对应的资源清单文件如下：

#master节点

cat  >> runner-statefulset.yaml << EOF

apiVersion: apps/v1

kind: StatefulSet

metadata:

  name: gitlab-ci-runner

  namespace: gitlab

  labels:

    app: gitlab-ci-runner

spec:

  updateStrategy:

    type: RollingUpdate

  replicas: 2

  serviceName: gitlab-ci-runner

  selector:

    matchLabels:

      app: gitlab-ci-runner

  template:

    metadata:

      labels:

        app: gitlab-ci-runner

    spec:

      volumes:

      - name: gitlab-ci-runner-scripts

        projected:

          sources:

          - configMap:

              name: gitlab-ci-runner-scripts

              items:

              - key: run.sh

                path: run.sh

                mode: 0755

      serviceAccountName: gitlab-ci

      securityContext:

        runAsNonRoot: true

        runAsUser: 999

        supplementalGroups: [999]

      containers:

      - image: hub.harbor.com/gitlab/gitlab-runner:v1.0

        name: gitlab-ci-runner

        command:

        - /scripts/run.sh

        envFrom:

        - configMapRef:

            name: gitlab-ci-runner-cm

        env:

        - name: RUNNER_NAME

          valueFrom:

            fieldRef:

              fieldPath: metadata.name

        ports:

        - containerPort: 9100

          name: http-metrics

          protocol: TCP

        volumeMounts:

        - name: gitlab-ci-runner-scripts

          mountPath: "/scripts"

          readOnly: true

      restartPolicy: Always

EOF

#### 5.4.2.4 创建一个名为gitlab-ci的serviceAccount，新建一个rbac资源清单文件
创建一个Service Account用于gitlab名称空间的身份验证和授权。

#master节点执行

cat >> runner-rbac.yaml << EOF

apiVersion: v1

kind: ServiceAccount

metadata:

  name: gitlab-ci

  namespace: gitlab

---

kind: Role

apiVersion: rbac.authorization.k8s.io/v1

metadata:

  name: gitlab-ci

  namespace: gitlab

rules:

  - apiGroups: ["*"]

    resources: ["*"]

    verbs: ["*"]

---

kind: RoleBinding

apiVersion: rbac.authorization.k8s.io/v1

metadata:

  name: gitlab-ci

  namespace: gitlab

subjects:

  - kind: ServiceAccount

    name: gitlab-ci

    namespace: gitlab

roleRef:

  kind: Role

  name: gitlab-ci

  apiGroup: rbac.authorization.k8s.io

EOF

#### 5.4.2.5 将管理员权限开放
将集权管理员权限开放给所有名称空间。

#master

cat >> gitlab-cluster-admin.yaml << EOF

apiVersion: rbac.authorization.k8s.io/v1

kind: ClusterRoleBinding

metadata:

  name: gitlab-cluster-admin

roleRef:

  apiGroup: rbac.authorization.k8s.io

  kind: ClusterRole

  name: cluster-admin

subjects:

- apiGroup: rbac.authorization.k8s.io

  kind: Group

  name: system:serviceaccounts

EOF

#### 5.4.2.6 上传runner相关镜像到harbor仓库
将我们需要的镜像传到harbor仓库。

#master节点执行

docker load -i gitlab-runner-helper1.tar.gz

docker load -i gitlab-runner-helper2.tar.gz

docker load -i kubectl.tar.gz

docker load -i mysql5.7.tar.gz

docker load -i wordpress.tar.gz

docker push hub.harbor.com/tools/kubectl:v1.0

docker push hub.harbor.com/wordpress/wordpress:v1.0

docker push hub.harbor.com/wordpress/mysql:v5.7

#node节点执行

docker load -i gitlab-runner-helper1.tar.gz

docker load -i gitlab-runner-helper2.tar.gz

#### 5.4.2.7 创建所有资源
创建我们之前编写的yaml资源清单。

#master节点执行。

kubectl create -f runner-configmap.yaml -f runner-rbac.yaml -f runner-scripts-configmap.yaml -f runner-statefulset.yaml -f gitlab-cluster-admin.yaml

![[1698990012089-604b4f3d-c3da-4ecc-84be-144040ca498e.png]]

图5-23 创建runner的所有资源

回到浏览器输入:http://192.168.100.100:30880/admin/runners 查看runner是否部署成功。

![[1698990012427-54b01137-9080-47a0-a7e2-6d83f97933ea.png]]

图 5-24 查看runner状态

## 5.5 开始编写CI项目
### 5.5.1 创建一个新项目
登录gitlab点击创建一个新项目，项目名称输入CICD，勾选公开项目，创建项目。

![[1698990012898-0f73cf00-67e7-4c3c-beb3-c21fa00aac8c.png]]

图 5-25 创建一个CICD项目

### 5.5.2 开始编写代码
#### 5.5.2.1 编写项目代码
编写一个用于kubectl部署的yaml资源清单文件，后续使用kubectl容器部署这个资源。

#master节点执行

mkdir  /root/CICD  #创建一个项目目录

cd /root/CICD

#编写yaml创建wordpress名称空间

cat >> namespace.yaml << EOF

apiVersion: v1

kind: Namespace

metadata:

  name: wordpress

EOF

#编写deploymnet资源，将wordpress与mysql部署到同一pod中

cat >> wordpress-deploy.yaml << EOF

apiVersion: apps/v1

kind: Deployment

metadata:

  labels:

    blog: wordpress

  name: wordpress

  namespace: wordpress

spec:

  replicas: 1

  selector:

    matchLabels:

      blog: wordpress

  template:

    metadata:

      labels:

        blog: wordpress

    spec:

      containers:

      - image: hub.harbor.com/wordpress/wordpress:v1.0

        name: wordpress

        env:

        - name: WORDPRESS_DB_HOST

          value: 127.0.0.1

        - name: WORDPRESS_DB_USER

          value: wordpress

        - name: WORDPRESS_DB_PASSWORD

          value: wordpress

        - name: WORDPRESS_DB_NAME

          value: wordpress

      - image: hub.harbor.com/wordpress/mysql:v5.7

        name: mysql

        env:

        - name: MYSQL_USER

          value: wordpress

        - name: MYSQL_PASSWORD

          value: wordpress

        - name: MYSQL_DATABASE

          value: wordpress

        - name: MYSQL_ROOT_PASSWORD

          value: "000000"

EOF

#编写service将wordpress服务端口暴露

cat >> wordpress-service.yaml << EOF

apiVersion: v1

kind: Service

metadata:

  labels:

    blog: wordpress

  name: wordpress

  namespace: wordpress

spec:

  ports:

  - port: 80

    protocol: TCP

    nodePort: 30080

  selector:

    blog: wordpress

  type: NodePort

EOF

#将mysql端口暴露

cat >> mysql-service.yaml << EOF

apiVersion: v1

kind: Service

metadata:

  labels:

    blog: wordpress

  name: wordpress-mysql

  namespace: wordpress

spec:

  ports:

  - port: 3306

    protocol: TCP

    targetPort: 3306

  selector:

name: mysql

EOF

#### 5.5.2.2 推送代码
编写完所有项目文件后我们使用git命令将代码推送到gitlab平台。

#master节点执行

#Git 全局设置

git config --global user.name "root"  #配置用户名

git config --global user.email [351719672@qq.com](mailto:351719672@qq.com)  #配置邮箱号

#使用当前文件夹

cd /root/CICD

git init   #初始化版本库

git remote add origin [http://192.168.100.100:30880/root/CICD.git](http://192.168.100.100:30880/root/CICD.git) #添加远程仓库

git add .   #将文件添加到本地仓库

git commit -m "推送代码"  #提交代码

git push -u origin master  #推送到远程仓库

    推送命令。

![[1698990013197-978ae575-cdca-4b27-b824-8e8386f92a7d.png]]

图 5-26 推送代码

 

### 5.5.3 编写流水线脚本触发CI自动部署
#### 5.5.3.1 .gitlab-ci.yml文件介绍
（1）.gitlab-ci.yml文件简介

GitLab CI通过YAML文件管理配置job，定义了job应该如何工作。该文件存放于仓库的根目录，默认名为.gitlab-ci.yaml。.gitlab-ci.yml文件中指定了CI的触发条件、工作内容、工作流程，编写和理解此文件是CI实战中最重要的一步，该文件指定的任务内容总体构成了1个pipeline、1个pipeline包含不同的stage执行阶段、每个stage包含不同的具体job脚本任务。当有新内容push到仓库，或者有代码合并后，GitLab会查找是否有.gitlab-ci.yml文件，如果文件存在，Runners将会根据该文件的内容开始build本次commit。

（2）Pipeline

一个.gitlab-ci.yml文件触发后会形成一个pipeline任务流，由gitlab-runner来运行处理。一次Pipeline其实相当于一次构建任务，里面可以包含很多个阶段（Stages），如安装依赖、运行测试、编译、部署测试服务器、部署生产服务器等流程。任何提交或者Merge Request的合并都可以触发Pipeline构建。

（3）Stages

Stages表示一个构建阶段，也就是上面提到的一个流程。我们可以在一次Pipeline中定义多个Stages，这些Stages会有以下特点：所有Stages会按照顺序运行，即当一个Stage完成后，下一个Stage才会开始只有当所有Stages完成后，该构建任务(Pipeline)才会成功如果任何一个Stage失败，那么后面的Stages不会执行，该构建任务(Pipeline)失败

（4）Jobs

Jobs表示构建工作，表示某个Stage里面执行的工作。我们可以在Stages里面定义多个Jobs，这些Jobs会有以下特点：相同Stage中的Jobs会并行执行、相同Stage中的Jobs都执行成功时，该Stage才会成功、如果任何一个Job失败，那么该Stage失败，即该构建任务(Pipeline)失败、一个Job被定义为一列参数，这些参数指定了Job的行为。下表列出了主要的Job参数：

表5-3 job参数详情

| 参数 | 是否必须 | 描述 |
| :---: | :---: | :---: |
| script | 是 | 由Runner执行的shell脚本或命令 |
| image | 否 | 用于docker镜像 |
| services | 否 | 用于docker服务 |
| stages | 否 | 定义构建阶段 |
| types | 否 | stages 的别名(已废除) |
| before_script | 否 | 定义在每个job之前运行的命令 |
| after_script | 否 | 定义在每个job之后运行的命令 |
| variable | 否 | 定义构建变量 |
| cache | 否 | 定义一组文件列表，可在后续运行中使用 |

#### 5.5.3.2 编写.gitlab-ci.yml文件
编写.gitlab-ci.yml文件，这个文件用于声明流水线需要运行的步骤和内容。

#master节点

cd  /root/CICD

cat >> .gitlab-ci.yml << EOF

image: hub.harbor.com/tools/kubectl:v1.0

#使用这个镜像

 

#配置文件内变量

variables:

  PROJECT_HUBURL: "http://192.168.100.100:30880/root/Graduation-project.git"

 

#配置运行顺序

stages:

  - deploy

 

before_script:

#  - git clone  "$PROJECT_HUBURL"

 

wordpress:

  stage: deploy

  script:

    - echo "开始部署wordpress"

    - ls -al /builds/root/CICD/

    - cd /builds/root/CICD/ && kubectl apply -f  namespace.yaml -f mysql-service.yaml -f  wordpress-deploy.yaml -f  wordpress-service.yaml

    - sleep 10 && kubectl get  -f mysql-service.yaml -f  wordpress-deploy.yaml -f  wordpress-service.yaml

- echo "wordpress部署成功，请访问30080端口查看服务"

EOF

#推送脚本到平台触发构建

git add .

git commit -m "push ci file"

git push

### 5.5.4  查看构建情况
查看流水线运行状态，浏览器输入。![[1698990013444-28eacea1-ac7a-4456-ac07-c1b4536cdec7.png]]

图 5-27 流水线

点击任务查看运行详情。![[1698990013666-6c9a9a46-b678-4e65-8d1b-2010ca273af0.png]]

图5-28 运行详情

### 5.5.5 访问wordpress
我们通过gitlab-ci自动化的在kubernetes平台部署了wordpress服务，我们可以在浏览器中访问，输入http://master:30080，选择简体中文。

![[1698990013958-0aa63089-20cd-4fbf-a666-08d98dc9ff74.png]]

图 5-29 设置简体中文

设置账号密码，安装wordpress。

![[1698990014201-af680c3c-43b4-487c-87b8-9630aa7c088a.png]]

图 5-30登录wordpress

![[1698990014565-973fed10-7b68-481e-ad5f-568d357d9126.png]]

图 5-31 dashboard

至此项目部署完毕！

 

六、总结

在整个毕业设计的过程中，我学到了许多宝贵的经验和知识。通过深入研究Kubernetes和GitLab CI，我不仅掌握了容器编排和持续集成/持续部署的原理，还获得了实际操作的技能。我明白了这些技术如何能够在现代软件开发中发挥关键作用，提高了团队的效率，降低了错误率，加速了交付，在项目中，我面临了各种挑战和困难，但正是这些挑战激发了我不断学习和改进的动力。同时，我要感谢我的指导教师，您的耐心和专业指导为我提供了宝贵的支持。您的建议和反馈帮助我克服了许多技术和方法上的障碍，使我能够朝着项目的成功完成迈进，通过这个毕业设计，我不仅获得了宝贵的学术和职业经验，还锻炼了自己的解决问题的能力，再次感谢您的支持和帮助，我对这个项目的成果感到自豪。希望我能够将所学到的知识和经验应用于未来的职业生涯中，并为科技领域的发展贡献一份微薄的力量。

# 参考文献
[1]夏丽娟,乌日娜,潘新.Kubernetes容器云编排系统的研究与实现[J].自动化应用,2023,64(14):224-227.

[2]梅松,赵春生,杨玉捷等.基于DevOps的精准测试体系构建与实践[J].软件,2023,44(07):125-127.

[3]郑有庆.Git在编程教学中的应用探究[J].铜陵职业技术学院学报,2023,22(02):96-100.DOI:10.16789/j.cnki.1671-752x.2023.02.019.

[4]陈晨. 基于Kubernetes的资源调度优化方法[D].北京建筑大学,2023.DOI:10.26943/d.cnki.gbjzc.2023.000532.

[5]陈鸣.基于Kubernetes的云平台健康监控与检查自愈[J].东南传播,2022(06):135-137.DOI:10.13556/j.cnki.dncb.cn35-1274/j.2022.06.016.

[6]张有帅,佘葭,尹雪龙.基于Kubernetes的容器云平台研究与设计[J].电子设计工程,2021,29(22):180-183+188.DOI:10.14022/j.issn1674-6236.2021.22.038.

[7]高鹏伟. 基于Kubernetes和Docker的容器云平台设计与实现[D].西安电子科技大学,2021.DOI:10.27389/d.cnki.gxadu.2021.002684.

[8]赵学作,赵少农.CentOS中Gitlab的安装与调试[J].网络安全和信息化,2021(01):107-108.

[9]黄嘉鑫. 基于Kubernetes的容器云平台设计与实现[D].西南石油大学,2020.DOI:10.27420/d.cnki.gxsyc.2020.000131.

[10]徐志平. 容器云平台Kubernetes集群管理系统的设计与实现[D].南京大学,2020.DOI:10.27235/d.cnki.gnjiu.2020.001643.

[11]王庆. 基于容器的DevOps云平台设计与实现[D].电子科技大学,2020.DOI:10.27005/d.cnki.gdzku.2020.003194.

[12]翁湦元,单杏花,阎志远等.基于Kubernetes的容器云平台设计与实践[J].铁路计算机应用,2019,28(12):49-53.

[13]汪信元. 基于Kubernetes的容器云平台设计和实现[D].武汉邮电科学研究院,2019.DOI:10.27386/d.cnki.gwyky.2019.000047.
